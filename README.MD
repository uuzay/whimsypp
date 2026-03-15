To create a simple, straight-to-the-point post:

```powershell
hugo new --kind minimal posts/minimal-post.md
```

To create a standard post with tags and Mermaid diagram support:

```powershell
hugo new --kind standard posts/standard-post.md
```

To create a deep dive post with a Table of Contents and Mermaid diagram support:

```powershell
hugo new --kind rich posts/rich-post.md
```
How to write a Mermaid diagram inside a post:

```
{{< mermaid >}}
graph TD;
    A[main.cpp] --> B{Compiler};
    B -->|Success| C[Binary];
    B -->|Fail| D[Compiler Error];
{{< /mermaid >}}
```

To preview the blog locally before pushing to GitHub:

```powershell
hugo server -D
```

Hosts on http://localhost:1313.

Categories for posts are (in increasing level):  
- modest
- remarkable
- diabolical
