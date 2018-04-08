## Example 6: Parallel mesh processing

{code:c#}
namespace Example6
{
    using System;
    using System.Collections.Concurrent;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.Linq;
    using System.Threading.Tasks;
    using TriangleNet;
    using TriangleNet.Geometry;
    using TriangleNet.IO;
    using TriangleNet.Meshing;

    class Program
    {
        static void Main(string[]() args)
        {
            Util.Tic();
            var polygons = LoadPolygons();
            Console.WriteLine("  Polygons: {0} (took {1}ms to load)", polygons.Count, Util.Toc());

            Util.Tic();
            RunSequential(polygons);
            Console.WriteLine("Sequential: {0}ms", Util.Toc());

            Util.Tic();
            RunParallel(polygons);
            Console.WriteLine("  Parallel: {0}ms", Util.Toc());
        }

        private static List<IPolygon> LoadPolygons()
        {
            string path = "c:/some/directory/with/poly/files";

            return Directory.EnumerateFiles(path, "*.poly", SearchOption.AllDirectories)
                .Select(file => FileProcessor.Read(file)).ToList();
        }

        class MeshResult
        {
            public int NumberOfTriangles { get; set; }
            public int Invalid { get; set; }
        }

        public static void RunParallel(List<IPolygon> polygons)
        {
            var queue = new ConcurrentQueue<IPolygon>(polygons);

            int concurrencyLevel = Environment.ProcessorCount;

            var tasks = new Task<MeshResult>[concurrencyLevel](concurrencyLevel);

            for (int i = 0; i < concurrencyLevel; i++)
            {
                tasks[i](i) = Task.Run(() =>
                {
                    // Each task has it's own triangle pool and predicates instance.
                    var pool = new TrianglePool();
                    var predicates = new RobustPredicates();

                    var config = new Configuration();

                    // The factory methods return the above instances.
                    config.Predicates = () => predicates;
                    config.TrianglePool = () => pool.Restart();

                    IPolygon poly;

                    var mesher = new GenericMesher(config);
                    var result = new MeshResult();

                    while (queue.Count > 0)
                    {
                        if (queue.TryDequeue(out poly))
                        {
                            var mesh = mesher.Triangulate(poly);

                            ProcessMesh(mesh, result);
                        }
                    }

                    pool.Clear();

                    return result;
                });
            }

            Task.WaitAll(tasks);

            int numberOfTriangles = 0;
            int invalid = 0;

            for (int i = 0; i < concurrencyLevel; i++)
            {
                var result = tasks[i](i).Result;

                numberOfTriangles += result.NumberOfTriangles;
                invalid += result.Invalid;
            }

            Console.WriteLine("Total number of triangles processed: {0}", numberOfTriangles);

            if (invalid > 0)
            {
                Console.WriteLine("   Number of invalid triangulations: {0}", invalid);
            }
        }

        public static void RunSequential(List<IPolygon> polygons)
        {
            var pool = new TrianglePool();
            var predicates = new RobustPredicates();

            var config = new Configuration();

            config.Predicates = () => predicates;
            config.TrianglePool = () => pool.Restart();

            var mesher = new GenericMesher(config);
            var result = new MeshResult();

            foreach (var poly in polygons)
            {
                var mesh = mesher.Triangulate(poly);

                ProcessMesh(mesh, result);
            }

            pool.Clear();

            Console.WriteLine("Total number of triangles processed: {0}", result.NumberOfTriangles);

            if (result.Invalid > 0)
            {
                Console.WriteLine("   Number of invalid triangulations: {0}", result.Invalid);
            }
        }

        private static void ProcessMesh(IMesh mesh, MeshResult result)
        {
            result.NumberOfTriangles += mesh.Triangles.Count;

            if (!MeshValidator.IsConsistent((Mesh)mesh))
            {
                result.Invalid += 1;
            }
        }
    }

    public static class Util
    {
        private static Stopwatch stopwatch = new Stopwatch();

        public static long Toc()
        {
            stopwatch.Stop();
            return stopwatch.ElapsedMilliseconds;
        }

        public static void Tic()
        {
            stopwatch.Restart();
        }
    }
}
{code:c#}