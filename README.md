# 🍽️ Restaurant Management System – SQL Project

This project is a comprehensive **Restaurant Management System** developed using **Oracle SQL**. It simulates real-world operations such as managing customers, restaurants, menu items, orders, and payments using SQL features like tables, triggers, procedures, and constraints.

## 📌 Features

- 👤 Customer registration and management
- 🏬 Restaurant and manager data handling
- 🍔 Menu item creation, update, and deletion
- 📦 Order placement and detail tracking
- 💳 Payment processing and updates
- 🔄 Triggers for auto-updates across tables
- 🔧 Procedures for dynamic insert, update, and delete operations

---

## 🗄️ Database Design

### 🧱 Tables Created:

- `customer`: Stores customer info
- `restaurant`: Basic details of restaurants
- `contact_info`: Stores contact numbers for restaurants
- `manager`: Restaurant managers and login credentials
- `menu_items`: Menu entries with price and status
- `orders`: Records of placed orders
- `order_details`: Items included in an order
- `payment`: Payment processing for orders

---

## 🧠 SQL Concepts Demonstrated

- **Primary & Foreign Keys**
- **Check Constraints**
- **Cascade/Delete Rules**
- **Stored Procedures** for:
  - Inserting/updating/deleting customers, restaurants, menu items
  - Placing and updating orders
- **Triggers** for:
  - Auto-updating totals and statuses
  - Syncing changes across dependent tables
- **Cursors** for fetching and displaying restaurant records

---

## 🔄 Example Procedures

- `INSERT_CUSTOMER`: Adds a new customer
- `place_order`: Handles order creation, order details, and payment
- `insert_order_detail`: Dynamically inserts an item into an existing order
- `insert_restaurant`, `delete_restaurant`, `update_restaurant`: Modify restaurant records
- `insert_menu_item`, `update_menu_item`, `delete_menu_item`: Manage menu entries

---

## ⚙️ Sample Trigger Logic

- **orders_customer_upd_trg**: Updates customer_id across orders
- **trg_order_details_upd**: Auto-updates `total_amount` in `orders`
- **update_payment_amount**: Syncs payments with updated order totals
- **trg_menu_items_upd**: Reflects item price changes in order details

---

## 📊 Sample Insert Data

Includes insert statements for:
- 5 customers
- 5 restaurants
- 5 managers
- Contact info for each restaurant
- 5 menu items with varied statuses
- A fully functional `place_order` call with all required data

---

## 📁 Project Structure

```
restaurant-db/
├── tables.sql # CREATE TABLE statements
├── inserts.sql # Sample data inserts
├── triggers.sql # Triggers to maintain integrity
├── procedures.sql # All stored procedures
├── test_cases.sql # Sample procedure executions
└── README.md # Project documentation
```


---

## ✅ How to Use

1. Run all `CREATE TABLE` statements first
2. Execute the `INSERT` statements to populate the database
3. Execute all `TRIGGERS` and `PROCEDURES`
4. Use test `BEGIN ... END;` blocks to simulate real transactions

---

## 👨‍💻 Developed By

**Abhiraj Singh Jhajj**

---

## 📌 Notes

- Developed and tested on **Oracle SQL**
- Can be adapted to other RDBMS with minor syntax modifications
- Designed with normalization and real-world constraints in mind
