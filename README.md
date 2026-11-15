# Assigment 3: Automated Parking Lot

Up to this point, our assignments have heavily focused on mathematical operations and low-level class interactions. Starting now, we are shifting our paradigm toward **System Design**.

In this assignment, you will build the core domain model for an automated parking lot system. You will need to utilize Inheritance, Abstract classes, Interfaces, and Collections to manage the lot's state and logic.


## Getting Started
### Requirements
- **Java 27** (JDK). Check with `java --version`. 
- **Apache Ant**. Check with `ant -version`. 

### Get the code
```bash
git clone -b assignment3 https://github.com/RonieKrasniker/OOP-Assignments.git
cd OOP-Assignments
```
`-b assignment3` checks out this assignment's branch.

### What you may change
- Write all your code in the `src/` folder.
- **Do not change anything outside `src/`.** This includes `build.xml`, the `checkstyle/`, `lib/` and `test/` folders.



## Part 0: Plan Your Design First (UML)

Before writing a single line of code, you must plan your design. Well-structured object-oriented systems are designed *before* they are implemented — building that habit is the point of this part.

Produce a **UML class diagram** that models the system described in the parts below. Your diagram must show:

* Every class and interface you intend to create (`Vehicle`, `Bike`, `Car`, `Truck`, `ParkingSpot`, `ParkingFloor`, `ParkingLot`, and any others you introduce).
* Each class's key fields and public methods.
* The **relationships** between them, using correct UML notation:
  * **Inheritance** — e.g. `Bike` / `Car` / `Truck` extending the abstract `Vehicle` — drawn with the hollow-triangle arrow.
  * **Composition / aggregation** — e.g. a `ParkingFloor` holds many `ParkingSpot`s and a `ParkingLot` holds many `ParkingFloor`s — annotated with multiplicities (`1`, `*`, `0..1`, etc.).

You may use any tool you like (draw.io, PlantUML, Mermaid, Lucidchart, …) or even draw it neatly by hand and photograph it. Submit it at the root of your project as `design.<ext>`. Acceptable forms are a rendered image (`.png` / `.pdf`), text-based source (`.puml` / `.md`), **or both** — e.g. a `.puml` source alongside its rendered `.png`. Submitting both the source and a rendered image is encouraged, since the source is easy to revise and the image is easy to read.

This diagram is a **required deliverable**. It does not need to match your final code perfectly — designs evolve as you implement — but it must be a genuine, complete plan of the system, not a picture reverse-engineered from finished code.

## Part 1: Vehicle Hierarchy

Your system must handle different types of vehicles. You will need to design an `abstract class Vehicle` that encapsulates the common properties of all vehicles.

Each `Vehicle` must have the following properties:

* Vehicle size (`int`)
* License plate (`String`)

Required public methods:

* `int getSize()` — return the vehicle size.
* `String getLicensePlate()` — return the license plate.

You must implement three concrete classes that inherit from `Vehicle`:

* `Bike`
* `Car`
* `Truck`

Each concrete vehicle type must have a fixed, hardcoded size that is assigned automatically during instantiation (the user of the class should only need to provide the license plate). The exact size values are up to you, but they must maintain the logical ordering: `Bike < Car < Truck`.

*(Hint: Think about how you will assign the specific size to each vehicle type during instantiation!)*

## Part 2: Parking Lot Architecture

You will build the structural representation of the parking lot using the following entities:

### `ParkingSpot`

Represents a single physical spot where a vehicle can be parked.

* Max vehicle size (`int`)
* Spot ID (`int`)
* Occupied status (`boolean`)

Constructor:

```java
public ParkingSpot(int maxVehicleSize, int spotId)
```

A newly created spot is unoccupied by default.

Required public methods:

* `int getMaxVehicleSize()` — return the max vehicle size this spot can hold.
* `int getSpotId()` — return the spot ID.
* `boolean isOccupied()` — return `true` if the spot is currently occupied.
* `void setOccupied(boolean occupied)` — set the occupied status of this spot.

### `ParkingFloor`

Represents a distinct level in the parking lot.

* Floor number (`int`)
* A Collection of parking spots (`ParkingSpot`)

Constructor:

```java
public ParkingFloor(int floorNumber)
```

A newly created floor has no parking spots.

Required public methods:

* `int getFloorNumber()` — return the floor number.
* `Collection<ParkingSpot> getSpots()` — return the collection of parking spots on this floor.
* `void addSpot(ParkingSpot spot)` — add a parking spot to this floor.

## Part 3: Business Logic & Core Methods

The entry-point of the system should be called `ParkingLot`.

`ParkingLot` must have a **default constructor** (no parameters) that initializes an empty lot. You must then provide methods to build up the lot's structure.

The hourly rate should be defined as a **public static constant** `HOURLY_RATE` inside the `ParkingLot` class.

Your `ParkingLot` class must implement the following `public` methods to interact with the system:

### 1. Getters

* `Collection<ParkingFloor> getFloors()` — return the collection of floors in the parking lot.

### 2. Adding a Floor

```java
public void addFloor(ParkingFloor floor)
```

Add a floor to the parking lot.

### 3. Parking a Vehicle

```java
public boolean parkVehicle(Vehicle v)
```

Attempt to park the provided vehicle in the parking lot. Return `true` if successful and `false` if not (e.g., if the lot is full or no adequately sized spot exists).

**Spot Allocation Rules:**
The parking spot assigned to the vehicle must be determined according to these strict rules:

1. Floors must be iterated in ascending order by their floor number.
2. Spots on a floor must be iterated in ascending order by their Spot ID.
3. The **smallest possible spot** that fits the vehicle must be used (to optimize space utilization).

### 4. Unparking a Vehicle & Calculating Fees

```java
public int unparkVehicle(String licensePlate, int durationInMinutes)
```

Unpark the vehicle associated with the given license plate. Return the calculated parking fee based on the hourly rate and the provided duration. If no vehicle with the given license plate is currently parked, return `-1`.

**Fee Calculation Rules:**

* If the vehicle is parked for 15 minutes or less, parking is entirely free.
* If the vehicle is parked for 16 minutes or more, each hour is paid in full. This includes partial hours (e.g., 2 hours and 1 minute will cost the exact same as 3 full hours).

### 5. Displaying Active Records

```java
public void displayParkingRecords()
```

Print all currently active parking records.
Think about storing parking records in a way that allows for easy display (retrieval) and easy insert preserving order

**Output Requirements:**

* Records **must** be printed in the exact same order that they were created (insertion order). *(Design constraint: carefully consider which Java Collection preserves insertion order while also supporting easy removal of a record from the middle.)*
* Use the format `<SpotID> : <Vehicle license plate>`. Extra spaces around the `:` are not critical, but **blank lines are**: print only the currently active records, with **no blank lines**. In particular, when a vehicle in the middle of the order is unparked, its record must disappear entirely and **not** leave a gap (an empty line) where it used to be. *(Think about why a naïve structure — e.g. a spot-indexed array — would print such gaps, and choose your collection accordingly.)*
* Each parking record should be printed on a separate line.
* *Note: The angle brackets `< >` are not part of the format.* For example: `12 : 859-05-734`.

## Submission

Implement `Vehicle`, `Car`, `Bike`, `Truck`, `ParkingSpot`, `ParkingFloor`, and
`ParkingLot`.

Submit a single **`Assignment3.zip`** file containing a top-level `src` directory with:

- `Vehicle.java`
- `Car.java`
- `Bike.java`
- `Truck.java`
- `ParkingSpot.java`
- `ParkingFloor.java`
- `ParkingLot.java`
- The provided `Main.java`, unchanged (without local practice checks).
- Any helper classes required by your implementation.

In addition, the ZIP must contain your **UML class diagram** name design.<ext> at the top level, next to `src/`. 

The ZIP should have this structure:

```text
Assignment3.zip
├── design.<ext>
└── src/
    ├── Vehicle.java
    ├── Car.java
    ├── Bike.java
    ├── Truck.java
    ├── ParkingSpot.java
    ├── ParkingFloor.java
    ├── ParkingLot.java
    ├── Main.java
    └── ... additional helper classes
```

Do not include local practice checks, compiled `.class` files, tests, libraries,
IDE settings, or the entire project directory.

Before submitting, run:

```bash
ant clean
ant check-all
ant test
```

Ensure that your code compiles, Checkstyle reports no errors, and all tests pass.

Submit through the Submit system and verify that you receive a confirmation
email stating that your submission passed the automated tests.
