# Basic interaction patterns

A softwarre is made of several **components**, that can interact in a **synchronous** or in an **asynchronous** way, and can be sharing CPUs or other component, as well as stay completely separated (if we have processes running on different CPUs, then the system is distributed).

There are several basic interaction patterns between components.
The **main patterns** are:
- **Request/Response**'
- **One-Way message**;
- **Publish/Subscribe**;
- **Event Notification**;
- **REQUEST/RESPONSE**
	The most common patter. It is based on **synchronous** comunication.
	Basically, the caller commits a request, and **wait** for a response.
	Example: Function calling another function, REST API, Frontend service quering backend, ecc.
- **ONE-WAY MESSAGE** (FIRE AND FORGET)
	Components sends **asynchronous** messages to a queue or any without waiting for answare;
	Example: android app sends logs, sensor reading, ecc.
- **PUBLISH/SUBSCRIBE**
	**Publisher** sends event to an intermediary (**broker**) on a specific **topic**.
	**Subscribers** receive notification to the topics they are subscripted to.
- **EVENT NOTIFICATION**
	Passive components that reacts only to **events**.
