# Editing DAGs concurrently

- Say you have a tree structure, like some JSON or a Figma file
- You want to support multiplayer
- Say you have parent pointers on your nodes
- Naively, you could end up with cycles in the resulting structure—two players concurrently reparent a pair of nodes under each other
- So you need to actively detect this and break such cycles
- This is also called out in [Figma’s multiplayer blog post](https://www.figma.com/blog/how-figmas-multiplayer-technology-works/)