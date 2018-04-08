# Options
The {{Behavior}} class controls the meshing behavior of Trianlge.NET.
## Public properties

|| Name || Type || Description ||
| **{{Quality}}** | {{bool}} | Turn quality meshing on/off. |
| **{{MinAngle}}** | {{double}} | Minimum angle constraint. |
| **{{MaxAngle}}** | {{double}} | Maximum angle constraint. |
| **{{MaxArea}}** | {{double}} | Global maximum area constraint. |
| **{{VarArea}}** | {{double}} | Consider per triangle area constraints. |
| **{{SteinerPoints}}** | {{int}} | Maximum number of Steiner points. |
| **{{NoBisect}}** | {{int}} | No new vertices on the boundary. |
| **{{ConformingDelaunay}}** | {{bool}} | Generate conforming Delaunay triangulations. |
| **{{Algorithm}}** | {{TriangulationAlgorithm}} | Triangulation algorithm. |
| **{{UseBoundaryMarkers}}** | {{bool}} | Use boundary markers. |
| **{{Convex}}** | {{bool}} | Create segments on the convex hull. |
## Remarks
* Setting {{MinAngle}}, {{MaxAngle}} or {{MaxArea}} will automatically turn quality on.
* The static property {{Verbose}} turn logging on/off.
* The static property {{NoExact}} will turn off exact arithmetic (faster, but likely to fail in general).