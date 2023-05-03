# 1. Reliable, Scalable, and Maintainable Applications

## 1.1 Non-Functional Requirements[^1]

Non-functional requirements include qualities such as performance, reliability, availability, scalability, maintainability, security, usability, and many others. These attributes are often critical for the success of a system and should be considered and prioritized alongside the system's functional requirements.

_Performance_, for example, refers to the system's ability to respond to user requests within an acceptable time frame. _Reliability_ refers to the system's ability to function correctly and consistently over time. _Scalability_ refers to the system's ability to handle increasing amounts of work or traffic without a decrease in performance. _Maintainability_ refers to the ease with which the system can be maintained, updated, or modified over time.

_Usability_ refers to the ease with which users can interact with the system and accomplish their tasks. _Security_ refers to the system's ability to protect against unauthorized access or attacks.

Non-functional requirements are often measured and evaluated using specific metrics or benchmarks, such as response time, availability percentage, or defect rate. These metrics can be used to track and monitor the system's performance over time and identify areas for improvement.

Overall, non-functional requirements are an important aspect of system design and should be considered and prioritized alongside the system's functional requirements.

## 1.2 Reliability

In the context of building data-intensive applications, reliability refers to the ability of a system to operate correctly even in the presence of faults[^2]. Chapter 1 of "Designing Data-Intensive Applications" by Martin Kleppmann provides an overview of the techniques and technologies used to achieve reliability in data systems.

The chapter discusses different types of faults that can occur in a system, such as hardware faults, software faults, and human errors. It introduces the concept of fault tolerance, which involves designing a system to continue functioning even when faults occur.

One technique for achieving fault tolerance is replication, which involves creating multiple copies of data or processes and distributing them across multiple machines. The chapter discusses different replication strategies, such as single-leader replication and multi-leader replication. It also introduces the concept of quorums, which are used to ensure consistency between replicas.

The chapter also discusses other techniques for achieving reliability, such as redundancy, isolation, and versioning. It introduces the CAP theorem, which states that it is impossible for a distributed system to simultaneously provide consistency, availability, and partition tolerance[^3]. The chapter explains the trade-offs involved in choosing between these attributes and discusses different strategies for balancing them.

Overall, the Reliability section of "Designing Data-Intensive Applications" emphasizes the importance of reliability in data systems and introduces techniques for achieving fault tolerance and consistency. The section lays the foundation for the rest of the book, which goes into more detail on the different technologies and architectures used to build reliable, fault-tolerant data systems.

## 1.3 Scalability

In the context of building data-intensive applications, scalability refers to the ability of a system to handle increasing amounts of work or traffic without a decrease in performance[^4]. Chapter 1 of "Designing Data-Intensive Applications" by Martin Kleppmann provides an overview of the techniques and technologies used to achieve scalability in data systems.

The chapter discusses the challenges of achieving scalability, such as the need for parallelism, load balancing, and partitioning. It introduces the concept of vertical scaling, which involves increasing the resources of a single machine to handle more work, and horizontal scaling, which involves distributing work across multiple machines[^5].

The chapter discusses different types of partitioning strategies, such as
The chapter discusses different types of partitioning strategies, such as range partitioning and hash partitioning[^6]. It explains how partitioning can help to achieve scalability by allowing data to be distributed across multiple machines and processed in parallel.

The chapter also introduces the concept of load balancing, which involves distributing work evenly across multiple machines to avoid overloading any one machine[^7]. It discusses different load balancing strategies, such as round-robin and least connections, and explains how load balancing can help to achieve scalability and improve performance.

Overall, the Scalability section of "Designing Data-Intensive Applications" emphasizes the importance of scalability in data systems and introduces techniques for achieving it. The section lays the foundation for the rest of the book, which goes into more detail on the different technologies and architectures used to build scalable, high-performance data systems.

## 1.4 Maintainability

In the context of building data-intensive applications, maintainability refers to the ability of a system to be easily maintained and updated over time[^8]. Chapter 1 of "Designing Data-Intensive Applications" by Martin Kleppmann provides an overview of the techniques and technologies used to achieve maintainability in data systems.

The chapter discusses the challenges of achieving maintainability, such as the need for modularity, abstraction, and encapsulation. It introduces the concept of software architecture, which involves breaking a system down into components that can be developed and maintained independently[^9]. It discusses different architectural styles, such as layered architecture and microservices architecture, and explains how they can help to achieve maintainability.

The chapter also introduces the concept of version control, which is a technique for tracking changes to source code over time[^10]. It discusses different version control systems, such as Git and Subversion, and explains how they can help to achieve maintainability by making it easier to track and manage changes to a system's codebase.

The chapter emphasizes the importance of testing and debugging in achieving maintainability[^11]. It introduces different types of testing, such as unit testing and integration testing, and explains how they can help to identify and prevent bugs in a system. It also discusses the importance of monitoring and logging, which can help to identify and diagnose problems in a running system[^12].

Overall, the Maintainability section of "Designing Data-Intensive Applications" emphasizes the importance of maintainability in data systems and introduces techniques for achieving it. The section lays the foundation for the rest of the book, which goes into more detail on the different technologies and architectures used to build maintainable, adaptable data systems.

[^1]: Sommerville, I. (2015). Software Engineering. 10th Edition. Pearson.

[^2]: Kleppmann, M. (2017). Designing Data-Intensive Applications. O'Reilly Media, Inc.

[^3]: Brewer, E. A. (2000). Towards robust distributed systems (abstract). In Proceedings of the Nineteenth Annual ACM Symposium on Principles of Distributed Computing (PODC '00).

[^4]: Kleppmann, M. (2017). Designing Data-Intensive Applications. O'Reilly Media, Inc.

[^5]: Bondi, A. B. (2000). Characteristics of scalability and their impact on performance. In Proceedings of the 2nd International Workshop on Software and Performance (WOSP '00).

[^6]: Kleppmann, M. (2017). Designing Data-Intensive Applications. O'Reilly Media, Inc.

[^7]: Cardellini, V., Casalicchio, E., Colajanni, M., & Yu, P. S. (1999). The state of the
