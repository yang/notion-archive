# Noria

- Incremental view maintenance, but partial—keyed by query parameter
- Motivating query: you're building github, and want to get the sum of the stars on a repo (join repo with sum of its stars)
- Build your partial view like this:

![Untitled](Noria%20d5a0ccef7524403d8ae6f2c731be2df7/Untitled.png)

- Construct your usual query execution DAG, but these nodes can persist intermediate results.
    - Example:
        
        ![Untitled](Noria%20d5a0ccef7524403d8ae6f2c731be2df7/Untitled%201.png)
        
    - Enables sharing: unrelated queries can benefit
    - Enables resuming from partial work: don't have to just re-run the original query blindly, e.g. maybe the AVG already exists
- Each node emits down to its parent its results.
    - Because SQL is set oriented, an aggregation operator like AVG will can simply take in a –OLDVAL and +NEWVAL tuple.
    - This takes advantage of [Commutativity](Commutativity%2067dc8334094f482d9214d37f069536c0.md)   in operators—we are sending an "undo"
- Related
    - Incremental view maintenance: analytics oriented
    - Caching systems: ad-hoc
    - Dataflow: also analytics oriented
    - vs. differential dataflow/materialize:
- Complications
    - Concurrency: not blocking queries in the face of updates
    - TODO didn't dive into this, but takes advantage of sending down explicit "invalidations" for records (instead of merely sending their complement)
- Commercialized in ReadySet