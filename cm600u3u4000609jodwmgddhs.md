---
title: "Updating Records: A Cautionary Tale"
seoTitle: "How Deleting & Reinserting Tanks Database Performance & Security"
seoDescription: "Learn why a “delete all and reinsert” approach hurts performance, risks data loss, and breaks REST best practices. Discover better ways to handle updates an"
datePublished: Fri Jan 17 2025 00:28:28 GMT+0000 (Coordinated Universal Time)
cuid: cm600u3u4000609jodwmgddhs
slug: updating-records-a-cautionary-tale
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737121001152/f6f891af-2aed-40a4-ad84-451c78e86d04.png
tags: technical-debt, performance, security, databases, best-practices, software-engineering, rest-api

---

I was working on a new project where we need to show users what’s changed with the contacts they follow. Users want to see when and where their contacts changed roles—whether it’s within the same organization or at a different one.

### The Surprise Discovery

As I started digging into the current data storage, I was shocked to learn that we don’t capture the date or time when records are created or updated. This made it impossible to figure out how many contacts had been updated in the last month or year. It also meant queries on millions of records were painfully slow, since there wasn’t a good way to filter or sort them.

There were optional columns like `effective_month_from`, `effective_year_from`, `effective_month_to`, and `effective_year_to`, but they weren’t very helpful for filtering or ordering.

### Why Timestamps Matter

It quickly became clear that adding created and updated timestamps would be a big improvement. With timestamps (and proper indexing), we could filter by date, track changes, and figure out exactly when a job position was updated. This is essential for my project, which needs to show contact updates from the last 7 days or month.

So, I created a migration file to add these columns and indexes. That part was easy. What I wasn’t ready for was the existing update logic.

### The Shocking Update Method

When I checked the code responsible for updating these records, I discovered a very strange approach. Instead of just updating a row with new data, whenever a job position is updated or a new one is created, the code:

1. Selects all related data for the contact
    
2. Deletes all that data
    
3. Reinserts everything from scratch
    

Needless to say, this is problematic:

* **Performance Nightmare**: Multiple database calls for every tiny update.
    
* **Risk of Data Loss**: Even with transactions, there’s a higher chance something could go wrong.
    
* **Complex Code**: I found a 600-line method handling these deletions and insertions for every relationship, even if nothing changed.
    

#### Security Concern

Because the system relies on re-inserting whatever appears in the payload, if a role is simply left out of that payload, it’s treated as deleted. A malicious user could include only the mandatory fields in a request and inadvertently (or intentionally) delete all other data for that contact.

#### REST Best Practices Out the Window

On top of that, this “all-in-one” approach completely ignores REST best practices: a single POST request is doing create, update, and delete operations. That’s a big no-no for clean, predictable APIs, where each HTTP verb typically corresponds to a distinct operation (POST for create, PUT/PATCH for update, DELETE for removal).

### Trying to Make Sense of It

After reading all 600 lines of that method, I was both tired and disappointed. In my 10 years as an engineer, I’ve never seen anything like it. The worst part was that my entire project depended on a fix that was out of scope—and risky to implement because it could break other systems.

Yes, I could have added some quick checks comparing database values to the incoming request, but that felt like pouring more concrete around the existing bad design. Instead, I tried minimal, low-impact changes that would let me proceed with adding timestamps, without making the situation worse.

### Conclusion

This experience hammered home the importance of:

1. **Capturing Timestamps**: Essential for filtering, auditing, and analytics.
    
2. **Updating Records Properly**: Avoid needless complexity and heavy-handed operations that risk data loss.
    
3. **Security Implications**: A “delete and re-insert” strategy can be easily manipulated if the payload omits data.
    
4. **RESTful Principles**: A single POST request shouldn’t handle create, update, and delete all at once.
    
5. **Maintainable Code**: A 600-line update method is a glaring red flag.
    

If you ever see code that deletes everything just to reinsert it, consider it a massive warning sign. Address it sooner rather than later—before it turns into an even bigger mess.