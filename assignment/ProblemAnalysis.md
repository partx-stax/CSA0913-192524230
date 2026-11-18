# Task 1 – Understand the Problem and Identify the Objects

## Inputs and outputs
Inputs are registration number, vehicle category, user category/pass validity, requested entry/exit operation, floor-slot configuration, entry and exit timestamps, and (for EVs) charging units. Outputs are an allocated floor and slot, unique ticket number, entry time, exit time, billable duration, fee, and occupancy status.

## Candidate classes and responsibilities
- **ParkingFacility**: owns floors/slots, allocates and releases slots, prevents duplicate active registrations.
- **ParkingSlot**: represents one numbered position, its floor, supported category and occupancy.
- **Vehicle (abstract)**: common registration number and fee contract.
- **TwoWheeler, Car, ElectricCar**: specialised vehicle rates; ElectricCar also supports charging.
- **ParkingTicket**: immutable ticket number, vehicle, slot and entry time; records exit and fee.
- **ParkingUser / Student / Staff / Visitor**: models pass eligibility and user category.
- **PricingRule**: encapsulates rate/slab calculations so pricing changes do not spread through vehicle classes.
- **ParkingService**: coordinates entry, exit, ticket lookup and billing.

Nouns become candidate classes because they have identity/state or a cohesive responsibility. Verbs such as allocate, release, enter, exit, calculateFee and validatePass become methods.

## Business rules
1. A slot holds at most one vehicle.
2. An active registration number can have only one ticket.
3. Ticket numbers are unique and never reused.
4. Allocation must satisfy vehicle/slot compatibility.
5. A slot is released only when a valid active ticket exits.
6. Duration cannot be negative; started-hour billing rounds upward.
7. Valid monthly passes for students/staff make parking free; visitors pay the applicable rate.
8. EV charging is charged separately from parking.
9. Invalid registrations, slots, pass dates and negative charging units are rejected.

## Assumptions
The sample implementation uses local system time, INR, whole-minute durations, and a first-started-hour slab followed by each additional started hour. Daily maximum is configurable and not applied in the basic demo. A pass is valid when its expiry date is not before the parking date. Charging fee is Rs.12/kWh in the supplied code exercise.

## Alternative: objects versus parallel arrays
With objects, each Vehicle keeps registration, type-specific behaviour and future attributes together. A registration search is O(n) in an unsorted collection; adding a field means changing Vehicle/its constructor/accessors once. With parallel arrays, the same index must be kept aligned across regNo[], type[], entryTime[]. Search is still O(n), but every insertion/deletion must update all arrays consistently. Adding an attribute requires another array and coordinated index maintenance. Both store O(n) references/data; objects have per-object header/reference overhead but substantially reduce synchronization errors.
