# Mesh Topology

If you create a mesh with Triangle.NET, the {{Triangles}} property allows you to access mesh topology. The property returns a collection of {{ITriangle}}. The interface provides the following properties and methods:

|| Name || Type || Description ||
| **{{P0, P1, P2}}** | {{int}} | Gets the triangle's vertex ids. |
| **{{N0, N1, N2}}** | {{int}} | Gets the triangle's neighbor ids. |
| **{{GetVertex(int)}}** | {{Vertex}} | Gets the vertex at given index (where index may be 0, 1 or 2). |
| **{{GetNeighbor(int)}}** | {{ITriangle}} | Gets the neighbor at given index (where index may be 0, 1 or 2). |
| **{{GetSegment(int)}}** | {{Segment}} | Gets the segment at given index (where index may be 0, 1 or 2). |
Triangle vertices are listed in counterclockwise order. While {{GetVertex}} will always return a proper Vertex object, a neighbor might not exist if the triangle lies on the boundary of a domain. In that case, the {{GetNeighbor}} method will return {{null}}. The same holds for the {{GetSegment}} method, which will return {{null}}, if the specified edge is not constrained or not on the boundary.

The segment returned by {{GetSegment(i)}} (if not {{null}}) is the edge opposite of vertex {{i}}.

![](Topology_topology.png)

From the above graphic you can see, that

* N0 is the neighbor opposite to P0 (across the edge P1-P2)
* N1 is the neighbor opposite to P1 (across the edge P2-P0)
* N2 is the neighbor opposite to P2 (across the edge P0-P1)

To see some code which uses the topology information of the {{ITriangle}} interface, have a look at the [Quadratic Elements](Quadratic-Elements) example.

Internally, Triangle.NET uses the same [structures](http://www.cs.cmu.edu/~quake/tripaper/triangle2.html) as the original Triangle code, i.e. the Otri struct (_oriented triangle_). To get a better understanding of how the Otri primitives work, open the Mesh Explorer, go to _Tools_ menu and select _Topology Explorer_.

