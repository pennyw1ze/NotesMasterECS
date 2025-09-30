# SysML

The Systems Modeling Language (SysML) is a standardized modeling language derived from UML, tailored for systems engineering. Unlike UML, which focuses primarily on software, SysML supports the specification, analysis, design, verification, and validation of a wide range of complex systems. It provides both structural and behavioral views, making it suitable for multidisciplinary engineering. SysML includes **diagrams** such as block definition diagrams, **internal block diagrams**, **parametric diagrams**, and **behavioral diagrams** (including activity, state machine, and sequence diagrams). For example, internal block diagrams describe the composition and connections of system components, while parametric diagrams capture constraints and equations relevant for engineering analysis. In practice, SysML helps engineers communicate, manage complexity, and ensure consistency across domains.

---

# ISO 12207, ISO 9000, ISO 25010

ISO 12207 defines the **software lifecycle processes**, covering activities from acquisition, development, and operation, to support processes such as configuration management and quality assurance. It is a process-oriented standard that specifies what should be in place, rather than how it should be implemented. ISO 9000, on the other hand, is a family of standards on **quality management systems**. It emphasizes principles such as customer focus, leadership, process approach, and continuous improvement. While not specific to software, it influences how organizations define quality practices. ISO 25010 focuses instead on **software product quality**, providing a quality model that includes characteristics such as functionality, reliability, usability, efficiency, maintainability, and portability. Together, these standards address complementary dimensions: lifecycle processes (12207), organizational quality systems (9000), and product quality attributes (25010).

---

# CMMI

The Capability Maturity Model Integration (CMMI) is a framework that assesses and guides **process improvement within organizations**. It defines **maturity levels**, each corresponding to a degree of process standardization and improvement. At Level 1, processes are ad hoc and unpredictable. At Level 2, processes are managed at the project level, including requirements management, project planning, and configuration management. At Level 3, processes become standardized across the organization, with an emphasis on training, organizational process focus, and risk management. Levels 4 and 5 introduce quantitative management and continuous process improvement, respectively. Each level is supported by a set of Process Areas that detail best practices. CMMI complements standards such as ISO 12207 by providing a roadmap for organizations to progress toward higher process maturity.

---

# Agile and Scrum

Agile is a **philosophy of software development** that emphasizes adaptability, collaboration, and delivery of value through iterative cycles. It is based on principles expressed in the Agile Manifesto, such as valuing individuals and interactions over processes and tools. Scrum is one of the most widely used Agile frameworks. It **organizes development into time-boxed iterations called sprints**, typically lasting two to four weeks. **Roles** include the **Product Owner**, responsible for prioritizing requirements in the product backlog; the **Scrum Master**, who facilitates the process and removes obstacles; and the **Development Team**, which self-organizes to deliver increments of the product. Scrum relies on **ceremonies** such as sprint planning, daily stand-ups, sprint review, and sprint retrospective. Its strength lies in fostering communication, transparency, and adaptability to change.

---

# DevOps

DevOps is the integration of software development and operations, combining cultural change with automation to deliver software faster and more reliably. Its goal is to increase velocity, reduce downtime, and minimize human error by creating shared responsibility across teams. The approach is iterative, moving from coding and building to testing, deployment, and monitoring, with feedback flowing back to developers.
Central to DevOps are **continuous integration**, which merges code frequently into a shared repository with automated builds and tests, and **continuous delivery**, which ensures the software is always in a releasable state. **Continuous deployment** extends this by automatically releasing validated builds to users. These practices are supported by **tools** for collaboration, planning, source control, issue tracking, configuration management, automated testing, and deployment. Together they create a streamlined pipeline that improves both speed and quality of delivery.

---

# SQUARE

The Security Quality Requirements Engineering (SQUARE) methodology is a structured **approach for** eliciting, categorizing, and **prioritizing security requirements**. It consists of nine steps, beginning with agreeing on definitions and identifying security goals, and ending with the inspection of requirements for consistency and completeness. The key idea is to integrate security requirements engineering early into the software lifecycle, reducing vulnerabilities and costly fixes later. SQUARE **promotes stakeholder involvement**, structured risk assessment, and **traceability** from threats to specific requirements. In practice, it strengthens the overall security posture of the system by making security a first-class concern rather than an afterthought.

---

# Domain-Driven Design and Microservices

Domain-Driven Design (DDD) is a **software design approach** that emphasizes aligning the structure and language of the code with the business domain. Central to DDD is the concept of the “ubiquitous language,” a shared vocabulary between developers and domain experts. DDD encourages decomposition of complex domains into bounded contexts, each of which can be developed independently. This approach aligns naturally with microservices architecture, where each bounded context may be implemented as a separate microservice. Microservices are **small, autonomous services** that communicate via lightweight protocols such as REST. They allow **scalability**, **resilience**, and technological **flexibility**, but require careful attention to service boundaries and inter-service communication. DDD provides the theoretical foundation for identifying the right boundaries, while microservices offer the practical implementation strategy.

---

# REST and SOAP

SOAP (Simple Object Access Protocol) and REST (Representational State Transfer) are two **paradigms** for **building web services**. SOAP is a protocol that relies on **XML-based** messaging and operates over several protocols, commonly HTTP. It is characterized by strict standards, support for security and transactions, and formal service contracts defined in WSDL. REST, in contrast, is an **architectural style** that leverages the HTTP protocol directly, using methods like GET, POST, PUT, and DELETE to operate on resources identified by URIs. REST emphasizes simplicity, statelessness, and scalability. Whereas SOAP is often used in enterprise systems requiring strong guarantees and interoperability, REST is preferred for lightweight, web-based APIs. The choice between SOAP and REST depends on system requirements, such as performance, complexity, and need for formal service contracts.

---

# CoCoMo / CoCoMo II and Function Points

The Constructive Cost Model (CoCoMo) is a cost estimation model developed by Barry Boehm. It **estimates** the **effort and cost of software projects** based primarily on the size of the software, measured in lines of code. The original CoCoMo defined three modes (organic, semidetached, embedded) and provided equations relating size to effort, duration, and staffing. CoCoMo II is an updated version that adapts to modern practices, accounting for reuse, rapid development, and different levels of detail during estimation (early design vs post-architecture). Function Point Analysis (FPA) is an **alternative approach to estimating software size and cost**. Instead of lines of code, it measures functionality delivered to the user, counting elements such as inputs, outputs, inquiries, and data files, weighted by complexity. FPA supports technology-independent comparisons and early estimation. Together, CoCoMo and Function Points represent two major schools of cost estimation: algorithmic models and functional size measurement.

---