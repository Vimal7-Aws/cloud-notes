# AWS RDS Read Replicas vs Multi-AZ Deployments








![image](https://github.com/user-attachments/assets/df087d80-b8b5-4671-8976-08c9fd9eba1b)

## Purpose

* **Multi-AZ DB Instance** deployments provide high availability, durability, and automatic failover support.
* **Read Replicas** enable increased scalability and database availability in the case of an AZ failure. Read Replicas allow elastic scaling beyond the capacity constraints of a single DB instance for read-heavy database workloads.

## Region & Availability Zones

* **Multi-AZ**: Automatically provisions and manages a standby instance in a different AZ within the same AWS region.
* **Read Replicas**: Can be provisioned within the same AZ, Cross-AZ, or even Cross-Region.

## Replication Mode

* **Multi-AZ**: Synchronous replication to a standby instance in a different AZ.
* **Read Replicas**: Asynchronous replication from the Primary instance.

## Standby Instance Read Access

* **Multi-AZ**: Standby instance does **not** support reads.
* **Read Replicas**: Can be used to serve **read** traffic.

## Automatic Failover & Failover Time

* **Multi-AZ**: Automatic failover with up to 120 seconds delay. DNS endpoint remains the same.
* **Read Replicas**: No automatic failover. Must manually promote a replica.

## Upgrades

* **Multi-AZ**: Engine upgrades happen on the primary instance.
* **Read Replicas**: Independent upgrade from the primary.

## Automated Backups

* **Multi-AZ**: Taken from the standby instance.
* **Read Replicas**: Backups are not enabled by default.

---

## AWS Certification Exam Practice Questions

> *Disclaimer: Questions are community-collected and may be outdated. AWS services evolve constantly. Always verify from the latest AWS documentation.*

### Q1

**Scenario**: Add a reporting tier to a multi-tier web app with a Multi-AZ RDS MySQL instance.

**Answer**: Launch an RDS Read Replica and generate reports by querying the replica.

### Q2

**Scenario**: Two-tier web app with complex queries and joins.

**Answer**: Amazon RDS for MySQL with Multi-AZ.

### Q3

**Scenario**: Social media site experiencing read contention.

**Answer**:

* Deploy ElastiCache in-memory cache.
* Add RDS MySQL read replicas.

### Q4

**Scenario**: Multi-regional logistics software, fast batch processing needed in HQ.

**Answer**: Use RDS MySQL with a master in each region and a read replica in the HQ region.

### Q5

**Question**: What happens if the primary Multi-AZ DB fails?

**Answer**: The DNS record (CNAME) is updated to point to the standby.

### Q6

**Scenario**: Analytics jobs causing contention.

**Answer**: Create RDS Read Replicas for analytics workload.

### Q7

**Question**: Will standby RDS be in the same AZ?

**Answer**: No.

### Q8

**Question**: Is Read Replica of a Read Replica supported?

**Answer**: Only with MySQL-based RDS.

### Q9

**Question**: Multi-AZ feature doesn’t help in?

**Answer**: Region outage.

### Q10

**Question**: Multi-AZ DB instance serves reads and writes?

**Answer**: Primary.

### Q11

**Question**: Can standby be used for reads/writes?

**Answer**: No.

### Q12

**Question**: Supported storage engine for Read Replicas?

**Answer**: InnoDB.

### Q13

**Question**: Which DB uses server mirroring for Multi-AZ?

**Answer**: MS SQL.

### Q14

**Question**: What happens to other replicas if one is promoted?

**Answer**: Others continue replicating from the original master.

### Q15

**Question**: What changes during Multi-AZ failover?

**Answer**: The CNAME is changed to point to standby.

### Q16

**Question**: How to get failover alerts?

**Answer**: Use `DescribeEvents`.

### Q17

**Question**: Does Read Replica keep backup window after promotion?

**Answer**: TRUE.

### Q18

**Question**: Will I be alerted on failover?

**Answer**: Only if SNS is configured.

### Q19

**Question**: Can I force a failover?

**Answer**: Yes.

### Q20

**Question**: How does failover stay seamless?

**Answer**: DNS changes to redirect traffic.

### Q21

**Question**: Part of Multi-AZ failover?

**Answer**: DNS record is changed to standby.

### Q22

**Question**: What won't cause Multi-AZ failover?

**Answer**: Autoscaling.

### Q23

**Question**: 10% writes, 90% reads RDS scaling?

**Answer**: Use Read Replicas.

### Q24

**Question**: How does Multi-AZ work?

**Answer**: Uses synchronous replication to a standby in a different AZ.

### Q25

**Scenario**: Disaster recovery across Singapore region.

**Answer**: Asynchronous replication.

### Q26

**Question**: High latency on small MySQL with Multi-AZ?

**Answer**: Taking snapshot from standby won't help.

### Q27

**Question**: Are Reserved Instances available for Multi-AZ?

**Answer**: Yes.

### Q28

**Question**: Replica stuck after failover?

**Answer**: Delete and recreate the replica.

### Q29

**Question**: Data transfer cost for replication?

**Answer**: Free.

### Q30

**Question**: MS SQL Multi-AZ mechanism?

**Answer**: Runs one DB and synchronously copies to standby.

### Q31

**Scenario**: RDS batch analysis slows production.

**Answer**: Create a Read Replica and use SNS or SQS for notifications.
