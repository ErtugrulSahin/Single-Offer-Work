Single Offer Contract on Soroban
What Does It Do?
Allows a user to submit one active offer at a time.

Offers include the offeror’s address, amount, price, and status.

The contract owner (or a specified admin) can accept or cancel offers.

Prevents users from submitting multiple simultaneous offers.

How It Works 
submit_offer:
Any user can call this function to submit a new offer, specifying the amount and price. If the user already has a pending offer, the contract will reject the new submission.

accept_offer:
Only the contract owner (set via set_owner) can accept an offer. When accepted, the offer status changes to "Accepted".

cancel_offer:
Only the offeror (the user who submitted the offer) can cancel their own pending offer.

view_offer:
Anyone can view the details and status of a specific user's offer.

set_owner:
This function can be called only once to set the contract owner (admin). After that, only the owner can accept offers.
Use Cases

Token sale or auction: Only one active offer per user, preventing spam and abuse.

Job or service marketplace: Users can submit one proposal at a time.

Any scenario where a single, manageable offer per user is needed.
