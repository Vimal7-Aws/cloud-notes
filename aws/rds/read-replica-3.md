


# ⚖️ RDS Read Replicas vs Multi-AZ

---

## ❓ What is Amazon RDS Read Replicas?


![image](https://github.com/user-attachments/assets/0fc00287-0d8b-4038-87ad-93e996aa26b9)


- Amazon enables you to create one or more read-only copies of your database instance.
- Replication can be within the same AWS Region or in a different AWS Region.
- Read replicas make it easy to scale out beyond the capacity of a single DB instance for read-only workloads.
- You can have up to five Read Replicas per master, each with its own DNS endpoint.
- Read replicas use asynchronous replication, so reads are eventually consistent.
- Applications must update the connection string to leverage read replicas.

---

## 🛠️ RDS Read Replicas — Use Cases

![image](https://github.com/user-attachments/assets/3e34c1c4-007b-427c-82f8-3ecc63ee4a12)


- You have a production database handling normal load.
- You want to run reporting or analytics applications.
- You create a Read Replica to run the new workload there.
- The production application is unaffected.
- Read replicas are used for **SELECT** statements only (not INSERT, UPDATE, DELETE).
- Useful for read-heavy workloads; excess read traffic is directed to read replicas.
- Business reporting or data warehousing queries run against read replicas instead of the primary DB.

---

## 💸 RDS Read Replicas Pricing

![image](https://github.com/user-attachments/assets/91a9f73b-f513-4745-aa13-5028614edced)

- Billed as a standard DB Instance at the same rates.
- No charge for data transfer between source DB and read replica within the same region.
- Cross-region replication incurs data transfer charges.

---

## 🛡️ What is Amazon RDS Multi-AZ (Disaster Recovery)?

- AWS provides **high availability** within a single AWS Region.
- Data is synchronously replicated to a standby in a different Availability Zone (AZ).
- Single DNS endpoint remains the same after failover — no manual app changes needed.
- Not used for scaling.
- **Note:** Read Replicas can be configured as Multi-AZ for disaster recovery (DR).

---

## ⚠️ RDS Multi-AZ — Failover Conditions

Failover automatically occurs if:

- Primary DB instance fails.
- Network connectivity loss to primary.
- Availability Zone outage or unresponsiveness.
- DB instance modified (e.g., server type change).
- OS software patching underway.
- Manual failover initiated via reboot with failover.

---

## 🔄 RDS — From Single-AZ to Multi-AZ
![image](https://github.com/user-attachments/assets/3a0e5abd-3ce6-4cde-90f8-ffff499cbea8)


- Modify your DB instance and select **Multi-AZ**.
- Zero downtime operation — no database stopping required.

### Behind the scenes:

- RDS takes an automatic snapshot.
- New DB restored from snapshot in another AZ.
- Synchronization established between primary and standby.

---
```

Would you like me to add anything else?
