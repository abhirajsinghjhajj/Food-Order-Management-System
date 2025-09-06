# Restaurant Ordering Database

**Short description**

This repository contains a MySQL database schema, table definitions, constraints, indexes, and stored procedures for a simple restaurant ordering system. The schema supports customers, restaurants, managers, menu items, orders, order details, payments, and helper procedures for inserting/updating/deleting records and managing transactional order placement.

---

## Table of contents

* [Features](#features)
* [Schema overview](#schema-overview)
* [Tables](#tables)
* [Stored procedures & triggers](#stored-procedures--triggers)
* [Transactions & consistency](#transactions--consistency)
* [Setup](#setup)
* [Examples (usage)](#examples-usage)
* [Testing](#testing)
* [Contributing](#contributing)
* [License](#license)

---

## Features

* Normalized relational schema using InnoDB.
* Referential integrity with `ON DELETE` / `ON UPDATE` rules.
* Basic constraints (UNIQUE, CHECK for quantity > 0).
* Indexed columns for common lookups (e.g., `restaurant_id`, `customer_id`, `item_id`).
* Stored procedures for common operations: insert/update/delete, recalculating totals, placing an order (transactional), and inserting order lines.
* Automatic recalculation of order totals and propagation to payments via `recalc_order_total`.

---

## Schema overview

Top-level entities:

* `customer` — customers using the system.
* `restaurant` — restaurants that provide menu items.
* `manager` — restaurant managers (linked optionally to restaurants).
* `menu_items` — items sold by restaurants.
* `orders` — order header records.
* `order_details` — order line items.
* `payment` — payment records for orders.
* `contact_info` — phone/contact entries for restaurants.

All tables use `ENGINE=InnoDB` to support transactions and foreign key enforcement.

---

## Tables

Detailed table responsibilities (quick):

* **customer** — stores personal details, contact and authentication fields. Email is unique.
* **restaurant** — stores a restaurant name and address.
* **contact\_info** — simple mapping of contact values to a restaurant (FK with `ON DELETE CASCADE`).
* **manager** — credentials and contact data for managers; `restaurant_id` is nullable and `ON DELETE SET NULL`.
* **menu\_items** — item name, price, availability status, and FK to `restaurant`.
* **orders** — order header with status, total\_amount, processed\_by (restaurant), customer, and order\_date.
* **order\_details** — composite primary key `(order_id, order_item_number)` to represent line ordering; each row stores item, quantity and amount. Quantity must be > 0.
* **payment** — payment records referencing orders and manager who processed the payment.

Indexes are placed on commonly queried foreign keys to improve lookup performance.

---

## Stored procedures & triggers

The schema includes the following stored procedures (high-level):

* `recalc_order_total(p_order_id)` — sums `order_details.amount` for the order and updates `orders.total_amount` and the related `payment.amount`.

* `insert_customer(...)` — convenience wrapper to insert a customer.

* `insert_restaurant(...)`, `update_restaurant(...)`, `delete_restaurant(...)` — basic CRUD for restaurants.

* `insert_menu_item(...)`, `update_menu_item(...)`, `delete_menu_item(...)` — basic CRUD for menu items.

* `place_order(...)` — transactional procedure that creates an order header, inserts one order detail row, recalculates totals and inserts a payment. It uses `START TRANSACTION` / `COMMIT` and `SELECT ... FOR UPDATE` to lock the menu item price while reading it.

* `insert_order_detail(...)` — insert an additional order line and recalculate totals.

> Note: Procedures use parameterized inputs and `LAST_INSERT_ID()` to return generated primary keys where appropriate.

---

## Transactions & consistency

* `place_order` is implemented as a transaction to ensure atomic creation of order, order details, and payment.
* `SELECT ... FOR UPDATE` is used when reading `menu_items.price` inside `place_order` to avoid price race conditions.
* Foreign keys have protective `ON DELETE`/`ON UPDATE` behaviors (CASCADE, SET NULL) chosen to preserve referential integrity based on business rules.

---

## Setup

1. Make sure you have MySQL / MariaDB installed and running.
2. Create a new database and run the SQL script (this repository's `.sql` file) or paste the SQL into your preferred client.

```sql
-- Example commands
CREATE DATABASE IF NOT EXISTS DB;
USE DB;
-- then execute the provided SQL script file (e.g. `schema.sql`)
```

3. Ensure your MySQL user has permissions to create databases, tables, and routines.

4. If you use a GUI client (MySQL Workbench, phpMyAdmin) you can import the `.sql` file directly.

---

## Examples (usage)

### Recalculate an order's total manually

```sql
CALL recalc_order_total(1);
```

### Place an order (example)

`place_order` parameters (in order):

1. `p_customer_id` INT
2. `p_processed_by` INT (restaurant id processing the order) — nullable
3. `p_order_date` DATETIME
4. `p_menu_item_id` INT (single menu item id to add)
5. `p_quantity` INT
6. `p_payed_by` VARCHAR(100)
7. `p_payment_processed_by` INT (manager id who processed payment)
8. `o_order_id` OUT INT (returned)
9. `o_payment_id` OUT INT (returned)

Example call from MySQL client (capture OUT values):

```sql
SET @o_order_id=0; SET @o_payment_id=0;
CALL place_order(1, NULL, NOW(), 10, 2, 'CARD-XXXX', 3, @o_order_id, @o_payment_id);
SELECT @o_order_id, @o_payment_id;
```

### Add an order detail to an existing order

```sql
CALL insert_order_detail(1, 10, 3);
```

---

## Testing

* Insert a few restaurants, menu items and customers.
* Use `place_order` to create orders and validate that `orders.total_amount` and `payment.amount` are updated automatically.
* Test `DELETE` behaviors (e.g., deleting a `restaurant` should cascade to `menu_items` and `contact_info`).
* Test constraint violations (e.g., inserting `order_details` with `quantity <= 0` should fail).

---

## Limitations & future improvements

* Authentication & password storage: current `password` columns are plain `VARCHAR(255)`. In production, store salted hashes and use an authentication layer outside DB procedures.
* Audit logging: consider adding created/updated timestamps and user audit tables.
* Soft deletes: use a `deleted` flag instead of cascading `DELETE` in some cases if you need history.
* More robust payment handling: add payment status, gateway integration, refunds, partial payments.
* Add triggers to automatically stamp `created_at` / `updated_at` timestamps.

---

## Contributing

Contributions are welcome! Please open issues for bugs or feature requests and submit pull requests for enhancements.

**Recommended workflow**

1. Fork the repo
2. Create a feature branch
3. Add tests (SQL test data / example scripts)
4. Open a pull request describing changes

---

## License

This project is released under the MIT License. See `LICENSE` for details.

---

If you want, I can also generate a `schema.sql` file with the exact SQL (sanitized for production), a simple ER diagram (PlantUML), or example seed data. Tell me which one you'd like next.
