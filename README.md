# Learning Practice: Black-Box Testing, Part 1

**Application:** North Vacation Booking
**Technique:** Equivalence Partitioning
**Author:** Eric Cashman

**Cost formula:** total = (nights × $200) + $50 cleaning fee + (guests over 5 × $25 × nights)

## Equivalence Partition Tables

### Number of nights

| Classification   | Partition    | Range or Condition  | Expected Result      |
| ---------------- | ------------ | ------------------- | -------------------- |
| Number of nights | Invalid low  | 0 or fewer nights   | `ValueError`         |
| Number of nights | Valid        | 1–31 nights         | Reservation accepted |
| Number of nights | Invalid high | More than 31 nights | `ValueError`         |

### Number of guests

| Classification   | Partition             | Range or Condition  | Expected Result                     |
| ---------------- | --------------------- | ------------------- | ----------------------------------- |
| Number of guests | Invalid low           | Fewer than 1 guest  | `ValueError`                        |
| Number of guests | Valid, standard rate  | 1–5 guests          | No additional guest fee             |
| Number of guests | Valid, additional fee | 6–12 guests         | Additional $25 per guest, per night |
| Number of guests | Invalid high          | More than 12 guests | `ValueError`                        |

### Check-in and checkout dates

| Classification | Partition                      | Range or Condition                   | Expected Result      |
| -------------- | ------------------------------ | ------------------------------------ | -------------------- |
| Dates          | Valid                          | Check-in is before checkout          | Reservation accepted |
| Dates          | Invalid, check-in after        | Check-in is after checkout           | `ValueError`         |
| Dates          | Invalid, same day              | Check-in is the same day as checkout | `ValueError`         |
| Dates          | Invalid, missing or not a date | Date is empty or not a real date     | `ValueError`         |

### Availability

| Classification | Partition                            | Range or Condition                                               | Expected Result      |
| -------------- | ------------------------------------ | ---------------------------------------------------------------- | -------------------- |
| Availability   | Available                            | Every night in the range is free                                 | Reservation accepted |
| Availability   | Partly unavailable                   | At least one night in the range is already booked                | `UnavailableError`   |
| Availability   | Entirely unavailable                 | Every night in the range is already booked                       | `UnavailableError`   |
| Availability   | Checkout date booked by someone else | Nights are free, but checkout is another guest's check-in date   | Reservation accepted |

## Test Cases

Existing booking for the availability tests: another guest has nights 08/20/2027 through 08/24/2027 (check-in 08/20/2027, checkout 08/25/2027).

| ID   | Classification | Partition                            | Input                                                       | Expected Result                         |
| ---- | -------------- | ------------------------------------ | ----------------------------------------------------------- | --------------------------------------- |
| TC01 | Nights         | Invalid low                          | 06/01/2027 to 06/01/2027 (0 nights), 2 guests               | `ValueError`                            |
| TC02 | Nights         | Valid                                | 06/01/2027 to 06/11/2027 (10 nights), 2 guests              | Accepted, total $2,050                  |
| TC03 | Nights         | Invalid high                         | 06/01/2027 to 07/06/2027 (35 nights), 2 guests              | `ValueError`                            |
| TC04 | Guests         | Invalid low                          | 06/01/2027 to 06/04/2027 (3 nights), 0 guests               | `ValueError`                            |
| TC05 | Guests         | Valid, standard rate                 | 06/01/2027 to 06/06/2027 (5 nights), 3 guests               | Accepted, no guest fee, total $1,050    |
| TC06 | Guests         | Valid, additional fee                | 06/01/2027 to 06/05/2027 (4 nights), 8 guests               | Accepted, total $1,150                  |
| TC07 | Guests         | Invalid high                         | 06/01/2027 to 06/04/2027 (3 nights), 15 guests              | `ValueError`                            |
| TC08 | Dates          | Valid                                | 08/10/2027 to 08/13/2027 (3 nights), 2 guests               | Accepted, total $650                    |
| TC09 | Dates          | Invalid, check-in after              | Check-in 08/13/2027, checkout 08/10/2027, 2 guests          | `ValueError`                            |
| TC10 | Dates          | Invalid, same day                    | Check-in and checkout both 08/10/2027, 2 guests             | `ValueError`                            |
| TC11 | Dates          | Invalid, missing or not a date       | Check-in empty, checkout 08/13/2027, 2 guests               | `ValueError`                            |
| TC12 | Availability   | Available                            | 08/10/2027 to 08/13/2027 (3 nights), 2 guests               | Accepted, total $650                    |
| TC13 | Availability   | Partly unavailable                   | 08/22/2027 to 08/27/2027, 2 guests (08/22–08/24 booked)       | `UnavailableError`                      |
| TC14 | Availability   | Entirely unavailable                 | 08/21/2027 to 08/23/2027, 2 guests (both nights booked)     | `UnavailableError`                      |
| TC15 | Availability   | Checkout date booked by someone else | 08/17/2027 to 08/20/2027 (3 nights), 2 guests               | Accepted, total $650 (checkout is not a reserved night) |

**Coverage:** 15 test cases cover all 15 partitions. One test per partition replaces testing every possible value.

## AI Transparency

AI was used for grammar and a rough draft. A real life human being were then reviewed and checked.

## Citation

GeeksforGeeks. (2026, July 3). _Equivalence partitioning method_. https://www.geeksforgeeks.org/software-engineering/equivalence-partitioning-method/