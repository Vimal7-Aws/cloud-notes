In DynamoDB, **indexes** are like alternate “lookup tables” that let you query data using attributes other than your primary key.

There are **two main types**: **Local Secondary Index (LSI)** and **Global Secondary Index (GSI)**.

---

## 1️⃣ Local Secondary Index (LSI)

### 📌 Definition

* An **LSI** is an index that **shares the same partition key** as the table’s primary key but allows a **different sort key**.
* **“Local”** means the index is scoped to the **same partition** as the base table.

### 🛠 How it Works

* Partition key → same as table
* Sort key → different from the table’s sort key
* You can **query by partition key** but sort/filter by the new sort key.

### 📦 Example

Main table:

```
Partition key: UserID
Sort key: OrderDate
```

LSI:

```
Partition key: UserID
Sort key: OrderAmount
```

This lets you:

* Get all orders for a `UserID` sorted by amount instead of date.

### ⚖️ Characteristics

* **Created at table creation** (cannot add later).
* Shares the same **throughput capacity** as the table.
* Size limit: each item’s total size in the LSI ≤ 10 GB per partition key.

---

## 2️⃣ Global Secondary Index (GSI)

### 📌 Definition

* A **GSI** is an index with **a completely different partition key and/or sort key** than the base table.
* **“Global”** means the index can span **across all partitions** in the base table.

### 🛠 How it Works

* You can choose **any attribute(s)** as the partition and sort key.
* Allows querying in a completely different access pattern than the base table.

### 📦 Example

Main table:

```
Partition key: UserID
Sort key: OrderDate
```

GSI:

```
Partition key: ProductID
Sort key: OrderDate
```

This lets you:

* Query all orders for a specific `ProductID`.

### ⚖️ Characteristics

* Can be added **anytime** after table creation.
* Has its **own provisioned capacity** (or On-Demand mode).
* Supports **eventual consistency** only.

---

## 📊 LSI vs GSI Comparison

| Feature           | LSI                        | GSI                             |
| ----------------- | -------------------------- | ------------------------------- |
| Partition Key     | Same as table              | Different from table (optional) |
| Sort Key          | Different from table       | Different from table (optional) |
| Creation Time     | At table creation only     | Can be created anytime          |
| Capacity Mode     | Shares table’s RCU/WCU     | Has its own RCU/WCU             |
| Consistency       | Supports strong & eventual | Only eventual                   |
| Size Limit per PK | 10 GB                      | No 10 GB limit                  |

---

## 💡 Quick Tip for Interviews

* **LSI** = same partition key, different sort key, only at creation time.
* **GSI** = different partition key, created anytime, eventual consistency only.

---

If you want, I can make a **simple DynamoDB index architecture diagram** that shows the table with both LSI and GSI so it’s crystal clear for visual learners. That’s how I usually explain it in system design interviews.





Alright — here’s a **Terraform example** of a DynamoDB table with:

* **Primary key** (Partition Key + Sort Key)
* **Local Secondary Index (LSI)** → same partition key, different sort key
* **Global Secondary Index (GSI)** → different partition key and optional sort key

---

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_dynamodb_table" "orders" {
  name           = "OrdersTable"
  billing_mode   = "PAY_PER_REQUEST" # On-demand
  hash_key       = "CustomerID"      # Partition key
  range_key      = "OrderDate"       # Sort key

  # Primary Key Attributes
  attribute {
    name = "CustomerID"
    type = "S" # String
  }

  attribute {
    name = "OrderDate"
    type = "S"
  }

  # Attributes used for LSI
  attribute {
    name = "OrderStatus"
    type = "S"
  }

  # Attributes used for GSI
  attribute {
    name = "ProductID"
    type = "S"
  }

  attribute {
    name = "ShipmentDate"
    type = "S"
  }

  # Local Secondary Index (LSI)
  # - Shares the same Partition Key (CustomerID) as main table
  # - Different Sort Key (OrderStatus)
  local_secondary_index {
    name               = "CustomerID-OrderStatus-index"
    projection_type    = "ALL" # can be KEYS_ONLY or INCLUDE
    range_key          = "OrderStatus"
  }

  # Global Secondary Index (GSI)
  # - Can have a different Partition Key and optional Sort Key
  global_secondary_index {
    name               = "ProductID-ShipmentDate-index"
    hash_key           = "ProductID"
    range_key          = "ShipmentDate"
    projection_type    = "ALL"
  }

  tags = {
    Environment = "dev"
    Project     = "OrderService"
  }
}
```

---

### 🔍 How This Works

| Index Type      | Partition Key | Sort Key       | Purpose                                                                     |
| --------------- | ------------- | -------------- | --------------------------------------------------------------------------- |
| **Primary Key** | `CustomerID`  | `OrderDate`    | Main table key — ensures uniqueness per customer/date.                      |
| **LSI**         | `CustomerID`  | `OrderStatus`  | Lets you query orders for a customer by status instead of date.             |
| **GSI**         | `ProductID`   | `ShipmentDate` | Lets you query orders for a product by shipment date, across all customers. |

---

💡 **Key Points**

* You can have **multiple LSIs**, but they must share the **same partition key** as the table.
* You can have **multiple GSIs** with **different partition keys** — much more flexible.
* LSIs are created **at table creation time only**. GSIs can be added later.

---

If you want, I can also give you an **example query in AWS CLI and Go** that uses the LSI and GSI so you see them in action. That will help make the differences click instantly.
