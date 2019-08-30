# Think/Pair/Share
   
In previous cohorts, we went over throttling, load balancers, queues and caches. Because coming up with an ERD and system APIs (for system design) and coming up with class diagrams and activity diagrams (for OOP design) would take up a lot of time and there is a lot of material in the lecture, we can reserve some time in the TC/A on Fridays to either add on ideas like caches or load balancers to the system design REACTO or to introduce some scenarios on how to apply system design principles or both.

  
Examples of what can be asked in a TC/A:

Assuming they did a REACTO with schema and design for some system (Twitter, Fandango, etc):

- Where and how can we introduce a load balancer(s) in the system?
- How might we be able to introduce a cache?

Other questions that can be asked based on the lecture:

- What is database replication? In what situation might it be useful?
- How would you protect your API from being abused?
- What is sharding? Why is it useful?
- Above questions with load balancers, cache apply here as well

Other ways of doing a TC/A:

- Ask the pairs to design/diagram a very high level system (Tinder, Twitter, etc). This doesn't need to have schemas or APIs but rather just a high level flow chart noting system flow from application to database. After they do this, a group can volunteer to present their idea.

# Example for the Fandango REACTO

## Discussion on Taking the Booking System to the Next Level

It's first come first serve but how can we handle multiple users wanting to book the same seat? We should try to implement something like a timed system where you can reserve your seats by clicking on them and those seats are locked (based on availability) for a specific amount of time, say 5 minutes. After 5 minutes are up, and the user doesn't purchase the seats, the seats are freed up for someone else to take them. If the user does purchase the seats, the seats become fully unavailable.

### Using Microservices for Reservation System

#### Microservice for Active Reservation

- We can keep the reservations in some sort of a cache (as well as a database) that when a seat is purchased, it is removed from the reservation list. But also, would release the seats if the expiry time passes.

#### Microservice for Waiting Users

- Same idea as above but we have a cache of users. The user who was waiting the longest would be "next in line" for the seats.
- Use web sockets to be updated on reservation status

