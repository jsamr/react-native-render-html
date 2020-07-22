# A Deterministic Approach to Embedded Content Scaling

> :gem: _This RFC is part of project Foundry._

## Problem Frame

The way embedded content is scaled for best user experience is flaky, partly
because of its complexity, partly because it is unspecified. In this section,
we introduce a set of definitions to understand the challenges surrounding
scaling of embedded content.

| Variable                     | Unit           | Definition                                                                      |
| ---------------------------- | -------------- | ------------------------------------------------------------------------------- |
| `windowDimensions`           | dpi            | The area of the device in which the UI elements can be drawn.                   |
| `embeddedMaxWidth`           | dpi            | A prop which defines the maximum width of embedded content.                     |
| `styleDimensions`            | dpi, css units | Dimensions values inherited from different styles and / or attributes.          |
| `boxDimensions`              | dpi            | The dimensions of the box in RN rendering tree containing the embedded content. |
| `embeddedDimensions`         | dpi            | The dimensions of the embedded content in RN rendering tree.                    |
| `embeddedPhysicalDimensions` | pixel          | The original dimensions of the embedded object.                                 |

![problem frame](img/001-problem-frame.svg)

The first challenge regarding those different variables implicated in the
rendering of embedded content are the units.

The consumer of the library might hold some assumptions depending on how he
expects the content to be rendered. In reality, there are different approaches
and library authors must take into account the variety of possibilities:

1. Some consumers will expect embedded content, and by extension any content,
   to _never_ overflow the horizontal axis. Let this policy be “_Adaptive_”.
   This policy favors end-user comfort over content authors strictness.
2. Other consumers will expect embedded content to be rendered whilst honoring
   strictly the inline styles as dictated by HTML authors, even if that means
   overflowing the horizontal axis. Let this policy be “_Directive_”. This policy
   favors content authors strictness over end-user comfort.

## Adaptive policy

In *adaptive* policy, the following rules are enforced:

```math
boxMaxWidth = min(embeddedMaxWidth, windowWidth) - contentInsetsHorizontal
```

One would assume the following assertions to be true:

1. The `styleDimensions` is equivalent to the `drawBoxDimensions`.
2. The `embeddedMaxWidth` is the maximum width of the `drawBoxDimensions`.
3. The `drawBoxDimensions` width is always smaller or equal to
   `windowDimensions` width.
