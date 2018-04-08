# Example 2 - Creating a ring polygon

This example will show how to define a polygon using contours with point and segment markers:

{code:c#}
namespace Examples
{
    using System;
    using System.Collections.Generic;
    using TriangleNet.Geometry;

    public static class Example2
    {
        public static IPolygon CreateRingPolygon(double r = 4.0, double h = 0.2)
        {
            var poly = new Polygon();

            var center = new Point(0, 0);

            // Radius should be at least 4.0.
            r = Math.Max(r, 4.0);

            // Outer contour.
            poly.Add(Circle(r, center, h, 3));

            // Center contour (internal).
            poly.Add(Circle((r + 2.0) / 2, center, h, 2));

            // Inner contour (hole).
            poly.Add(Circle(2.0, center, h, 1), center);

            return poly;
        }

        /// <summary>
        /// Create a circular contour.
        /// </summary>
        /// <param name="r">The radius.</param>
        /// <param name="center">The center point.</param>
        /// <param name="h">The desired segment length.</param>
        /// <param name="label">The boundary label.</param>
        /// <returns>A circular contour.</returns>
        public static Contour Circle(double r, Point center, double h, int label = 0)
        {
            int n = (int)(2 * Math.PI * r / h);

            var points = new List<Vertex>(n);

            double x, y, dphi = 2 * Math.PI / n;

            for (int i = 0; i < n; i++)
            {
                x = center.X + r * Math.Cos(i * dphi);
                y = center.Y + r * Math.Sin(i * dphi);

                points.Add(new Vertex(x, y, label));
            }

            return new Contour(points, label, true);
        }
    }
}
{code:c#}
