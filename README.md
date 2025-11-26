# Intelligent Parking System 🚗🅿️

A Spring Boot–based **Intelligent Parking System** that digitally manages parking slots and vehicles.  
It exposes REST APIs and includes a simple Tailwind CSS dashboard to visualize and control the parking lot in real time.

---

## 📘 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [ER Diagram & Workflow](#er-diagram--workflow)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Clone the Repository](#clone-the-repository)
  - [Backend Setup](#backend-setup)
  - [Frontend / UI](#frontend--ui)
- [Running the Application](#running-the-application)
- [API Endpoints](#api-endpoints)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
  - [Parking Workflow](#parking-workflow)
  - [Unparking Workflow](#unparking-workflow)
- [Testing](#testing)
- [Future Enhancements](#future-enhancements)
- [License](#license)
- [Author](#author)

---

## Overview

The **Intelligent Parking System (IPS)** is a web application that manages a parking lot digitally.  
It models each **parking slot** and **vehicle** as entities and automates:

- Slot initialization (normal, handicap, EV)
- Parking of incoming vehicles
- Unparking and releasing slots
- Displaying live slot status on a dashboard

The project is designed as a **learning-friendly** yet **industry-style** Spring Boot app that can later be connected to sensors, a mobile app, or payment gateways.

---

## Features

- **Digital Parking Lot Representation**
  - Each slot is stored in the database with type, occupancy, and reservation status.
- **Automatic Slot Allocation**
  - Picks the most suitable free slot based on vehicle type and slot category.
- **Park / Unpark Operations**
  - REST endpoints to park a vehicle and unpark it.
- **Real-Time Status Dashboard**
  - Tailwind CSS UI to visualize slots (free/occupied/reserved).
- **Extensible Architecture**
  - Service-oriented, ready for IoT, payment, and analytics integration.

---

## Architecture

High-level layers:

1. **Client Layer**
   - HTML + Tailwind CSS + Vanilla JS dashboard.
2. **REST API Layer**
   - `ParkingController` exposes endpoints for status, park, and unpark.
3. **Service Layer**
   - `ParkingManager` implements all parking logic and rules.
4. **Data Access Layer**
   - Spring Data JPA repositories (`ParkingSlotRepository`, `VehicleRepository`).
5. **Database Layer**
   - H2 (in-memory) or MySQL for persistent storage.

You can include a simple architecture diagram here:

```kotlin
                ┌───────────────────────┐
                │   Browser Dashboard   │
                │  (HTML + Tailwind +   │
                │     JavaScript)       │
                └──────────┬────────────┘
                           │  HTTP (REST)
                           │
                ┌──────────▼────────────┐
                │   Spring Boot App     │
                │  (Intelligent Parking │
                │        System)        │
                └──────────┬────────────┘
                           │
      ┌────────────────────┼─────────────────────┐
      │                    │                     │
┌─────▼─────┐       ┌──────▼────────┐      ┌─────▼─────┐
│ Controller│       │  Service      │      │  Repos /  │
│  Layer    │       │  Layer        │      │  DAO      │
│(REST APIs)│       │ ParkingManager│      │(JPA Repos)│
└─────┬─────┘       └──────┬────────┘      └─────┬─────┘
      │                    │                     │
      │ calls              │ business logic      │ CRUD
      │                    │                     │
      │              ┌─────▼─────┐               │
      │              │   Model   │               │
      │              │ Entities  │               │
      │              │(Vehicle,  │               │
      │              │ParkingSlot│               │
      │              └─────┬─────┘               │
      │                    │                     │
      └────────────────────┼─────────────────────┘
                           │
                     ┌─────▼──────┐
                     │  Database  │
                     │ (H2/MySQL) │
                     └────────────┘


Optional / Future Integrations (outside main box):

   ┌──────────────────┐        ┌───────────────────┐
   │   Mobile App     │        │  IoT Sensors      │
   │(User Booking UI) │        │(Slot Occupancy)   │
   └───────┬──────────┘        └─────────┬─────────┘
           │       REST APIs / MQTT      │
           └────────────┬────────────────┘
                        │
                        ▼
                 Spring Boot App
```

---

## ER Diagram & Workflow

![Figure](./docs/er-diagram.png)
**Fig.:** ER Diagram and Workflow of Intelligent Parking System

**Entities**

- Vehicle
  - `licensePlate` (PK)
  - `vehicleType`
  - `entryTime`
  - `assignedSlotId`

- ParkingSlot
  - `slotId` (PK)
  - `slotSize`
  - `isOccupied`
  - `isReserved`
  - `vehicle` (FK → `Vehicle.licensePlate`)

**Relationship**

- `Vehicle` parked_in `ParkingSlot`
  → One-to-One relationship (a slot holds at most one vehicle at a time).

The diagram also illustrates the workflow:

1. Vehicle arrives and sends a park request.
2. System finds optimal slot based on type and availability.
3. Vehicle is assigned to the slot and status is updated.
4. On exit, vehicle is unparked, slot is released, and (optionally) fee is computed.

---

## Tech Stack

- **Language:** Java
- **Backend Framework:** Spring Boot
- **Build Tool:** Maven
- **Persistence:** Spring Data JPA
- **Database:** H2 (default) or MySQL
- **Frontend:** HTML5, Tailwind CSS, Vanilla JavaScript
- **Other:** Lombok (optional), Jakarta Persistence annotations

---

## Getting Started

**Prerequisites**
  - Java 17 or later
  - Maven 3.x
  - (Optional) Node.js if you want to build Tailwind via CLI
  - Git
  - A modern browser (Chrome / Edge / Firefox)

**Clone the Repository**
```bash
git clone https://github.com/Souvikdas040/intelligent-parking-system.git
cd intelligent-parking-system
```

**Backend Setup**
  1. Open the project in your IDE (IntelliJ / Eclipse / VS Code).
  2. Ensure Maven downloads all dependencies.
  3. Configure the database:

**Option 1 – H2 (In-Memory, default)**

**In ```src/main/resources/application.properties```:**
```vscode
spring.datasource.url=jdbc:h2:mem:parkingdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
```
