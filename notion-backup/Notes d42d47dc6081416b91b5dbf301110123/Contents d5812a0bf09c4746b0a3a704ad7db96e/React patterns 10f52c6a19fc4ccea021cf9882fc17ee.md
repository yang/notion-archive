# React patterns

- Send signal to some component to do something, from anywhere else in the app.
    - Example: Make a left panel component that has some “selectedValue” state change its value.
    - Non-solutions:
        - Lifting the state all the way to the root. This would concentrate a lot of the state management logic, which could have other local dependencies.
    - Solutions:
        - Dispatch a signal (from the root of the app) that is reacted to by the component.
    - Related:
        - Make a component (like a Select) switch to a value, but let it be an uncontrolled state.
            - You can’t! It must be a controlled state, or else you have to remount it with a new key.
- Signal a component do something once.
    - Example: Make a component “flash” or “ping” (or even perform a sequence, such as scroll-into-view-then-flash).
    - Non-solutions:
        - Use state. It’s not state. If you try to immediately set some state like `flashing` , and then immediately clear that state…when should you clear it? At the very least it’s inefficient.
    - Solutions:
        - Dispatch a signal
- Gather some state centrally from a tree of components that are reporting their state up.
    - Example: forms.