
Step 1: Identify Conditions and Actions
Five Conditions
1. Valid dates: The check-in date must be before the checkout date.
2. Valid number of nights: The reservation must be between 1 and 31 nights.
3. Valid number of guests: The reservation must include between 1 and 12 guests.
4. Availability: Every night in the requested date range must be available.
5. Extra guests: The reservation has more than five guests.

Five Actions
1. Create the reservation.
2. Calculate the standard reservation price of $200 per night plus a $50 cleaning fee.
3. Add $25 per night for each guest over five.
4. Raise a ValueError when reservation information is invalid.
5. Raise an UnavailableError when any requested night is unavailable.

Step 2: Create a Decision Table
Use T for true, F for false, - when the condition does not affect the result, and X when an action occurs.
Conditions and Actions	Rule 1	Rule 2	Rule 3	Rule 4	Rule 5	Rule 6
Dates are valid	T	T	T	F	T	T
Number of nights is valid	T	T	T	-	F	T
Number of guests is valid	T	T	T	-	-	T
Requested nights are available	T	T	F	-	-	T
More than five guests	F	T	-	-	-	-
Create reservation	X	X				X
Calculate standard price	X	X				X
Add extra guest fee		X				
Raise ValueError				X	X	
Raise UnavailableError			X

Explanation of the rules:
- Rule 1: A valid reservation with five or fewer guests is created without an extra guest fee.
- Rule 2: A valid reservation with more than five guests is created with the additional guest fee.
- Rule 3: The reservation is rejected because at least one requested night is unavailable.
- Rule 4: The reservation is rejected because the check-in and checkout dates are invalid.
- Rule 5: The reservation is rejected because the number of nights is outside the allowed range.
- Rule 6: A valid reservation with an available date range and a valid number of guests is created. The standard price is calculated.

Step 3: Create Decision Table Test Cases

TC01: Valid Reservation With Three Guests
- Rule: 1
- Check-in: October 1, 2026
- Checkout: October 4, 2026
- Number of guests: 3
- Availability: All nights available
- Expected action: Create reservation and calculate standard price.
- Expected price: $650
  - Three nights: 3 × $200 = $600
  - Cleaning fee: $50

TC02: Valid Reservation With Seven Guests
- Rule: 2
- Check-in: October 1, 2026
- Checkout: October 4, 2026
- Number of guests: 7
- Availability: All nights available
- Expected action: Create reservation and add extra guest fees.
- Expected price: $800
  - Three nights: $600
  - Cleaning fee: $50
  - Two extra guests: 2 × $25 × 3 = $150

TC03: Reservation With Unavailable Dates
- Rule: 3
- Check-in: October 1, 2026
- Checkout: October 4, 2026
- Number of guests: 3
- Availability: October 2 is unavailable.
- Expected action: Reject the reservation.
- Expected result: UnavailableError

TC04: Invalid Check-In and Checkout Dates
- Rule: 4
- Check-in: October 5, 2026
- Checkout: October 4, 2026
- Number of guests: 3
- Availability: All nights would otherwise be available.
- Expected action: Reject the reservation.
- Expected result: ValueError because check-in is after checkout.

TC05: Reservation Exceeds the Maximum Number of Nights
- Rule: 5
- Check-in: October 1, 2026
- Checkout: November 3, 2026
- Number of guests: 3
- Number of nights: 33
- Availability: All nights available.
- Expected action: Reject the reservation.
- Expected result: ValueError because the reservation exceeds 31 nights.

TC06: Invalid Number of Guests
- Rule: 6 (corrected to represent invalid guest information)
- Check-in: October 1, 2026
- Checkout: October 4, 2026
- Number of guests: 13
- Availability: All nights available.
- Expected action: Reject the reservation.
- Expected result: ValueError because the maximum allowed is 12 guests.

Step 4: Identify Reservation States and Events

Five Reservation States
1. Pending — Reservation has been created and is awaiting payment.
2. Confirmed — Payment has succeeded.
3. Checked In — The guest has checked in on the reservation date.
4. Completed — The guest has checked out.
5. Cancelled — The reservation has been cancelled or payment failed.

Five Events That Change Reservation States
1. Payment succeeds.
2. Payment fails.
3. Guest cancels the reservation.
4. Guest checks in.
5. Guest checks out.

Step 5: Create a State Transition Table

Current State	Event	Expected State	Expected Result
Pending	Payment succeeds	Confirmed	Reservation is confirmed.
Pending	Payment fails	Cancelled	Reservation is cancelled and dates are released.
Pending	Guest cancels	Cancelled	Reservation is cancelled.
Confirmed	Guest checks in on check-in date	Checked In	Guest is checked in.
Confirmed	Guest cancels	Cancelled	Reservation is cancelled and dates are released.
Checked In	Guest checks out	Completed	Reservation is completed.
Cancelled	Guest attempts to check in	Cancelled	Raise InvalidStateError.
Completed	Guest attempts to cancel	Completed	Raise InvalidStateError.
Cancelled	Payment succeeds	Cancelled	Raise InvalidStateError.
Pending	Guest attempts to check in	Pending	Raise InvalidStateError.
Completed	Guest attempts to check in	Completed	Raise InvalidStateError.

The table includes six valid transitions and five invalid transitions. For invalid transitions, the reservation remains in its original state and the application raises an InvalidStateError

Step 6: Create State Transition Test Cases

Valid State Transitions
Test ID	Starting State	Event	Expected State	Expected Result	Type
ST01	Pending	Payment succeeds	Confirmed	Reservation is confirmed.	Valid
ST02	Pending	Payment fails	Cancelled	Reservation is cancelled and dates are released.	Valid
ST03	Pending	Guest cancels	Cancelled	Reservation is cancelled.	Valid
ST04	Confirmed	Guest checks in on check-in date	Checked In	Guest is checked in.	Valid
ST05	Confirmed	Guest cancels	Cancelled	Reservation is cancelled.	Valid
ST06	Checked In	Guest checks out	Completed	Reservation is completed.	Valid

Invalid State Transitions
Test ID	Starting State	Event	Expected State	Expected Result	Type
ST07	Cancelled	Guest attempts to check in	Cancelled	Raise InvalidStateError.	Invalid
ST08	Completed	Guest attempts to cancel	Completed	Raise InvalidStateError.	Invalid
ST09	Cancelled	Payment succeeds	Cancelled	Raise InvalidStateError.	Invalid
ST10	Pending	Guest attempts to check in	Pending	Raise InvalidStateError.	Invalid
ST11	Completed	Guest attempts to check in	Completed	Raise InvalidStateError.	Invalid

Complete Reservation Sequence
Test ID: ST12
- Starting state: Pending
- Events: Payment succeeds → Guest checks in on the check-in date → Guest checks out.
- Expected transitions: Pending → Confirmed → Checked In → Completed
- Expected result: The reservation progresses through every valid stage and finishes in the Completed state.
- Type: Valid sequence
Cancellation Sequence
Test ID: ST13
- Starting state: Pending
- Event: Guest cancels the reservation.
- Expected state: Cancelled
- Expected result: The reservation is cancelled, the dates are released, and further state changes raise InvalidStateError.
- Type: Valid cancellation sequence

Conclusion
This assignment helped me understand how decision tables and state transition testing can be used to test an application without looking at its code. The decision table helped me identify different reservation scenarios, including valid bookings, invalid information, and unavailable dates. The state transition table helped me test how reservations change from pending to confirmed, checked in, completed, or cancelled. These test cases can help identify errors and make sure the North Vacation Booking application behaves as expected.
