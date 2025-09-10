# Step 16 - Serialization and Interceptors

## Introducing Interceptors and Serialization
![interceptors](./images/intro-interceptors.png)

**Explanation:**
Interceptors in NestJS are executed twice in the request-response lifecycle: once before the controller method runs, and once after the controller returns a response. This unique placement allows interceptors 


![life cycle](./images/interceptors-life-cycle.png)

to:
- Transform or filter outgoing responses (e.g., remove sensitive fields like passwords or Google IDs)
- Bind extra logic before/after method execution
- Alter exceptions returned by controllers
- Wrap responses in a data property or add metadata like API version

Interceptors help you apply consistent logic across your app without modifying every controller. In this section, you'll create interceptors to filter sensitive properties and globally wrap responses.



- Serializing User Entity
- Global Data Interceptor
- Adding API Version