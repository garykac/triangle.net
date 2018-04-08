![Mesh Lake Superior](wiki/Home_mesh-superior.png)

# Triangle.NET (by Christian Woltering)

With the shutdown of [Codeplex](https://archive.codeplex.com/) in 2017, [Christian Woltering](https://github.com/wo80)'s [Triangle.NET](https://archive.codeplex.com/?p=triangle) project was placed in read-only mode and archived.

Since the shutdown announcement, a number of snapshots have been made:

* [Chaosed0/DelaunayUnity](https://github.com/Chaosed0/DelaunayUnity) - Feb 2017, source code only + Unity support
   * Author wrote companion post [Delaunay Triangulation for Terrain Generation in Unity](https://straypixels.net/delaunay-triangulation-terrain/)
* [akopetsch/triangle-unity](https://github.com/akopetsch/triangle-unity) - Apr 2017, source code only + Unity support
* [Ranguna/Triangle-NET-Unity-Port](https://github.com/Ranguna/Triangle-NET-Unity-Port) - Nov 2017, source code only + Unity support

These are in addition to the ones that had already been created:

* [zon/triangle](https://github.com/zon/triangle) - source code only, based on Beta 3 + Unity support
* [parahunter/triangle-net-for-unity](https://github.com/parahunter/triangle-net-for-unity) - source code only, based on Beta 3 + Unity support
* [eppz/Triangle.NET](https://github.com/eppz/Triangle.NET) - source code only, apparently auto-synced to the latest version on Codeplex SVN while it was active

And now this one (sigh, cue relevant [XKCD comic](https://xkcd.com/927/)).

One thing missing from these other releases is the documentation that was present on the original Codeplex site. With the project now being archived, these documents are no longer readily accessible on the web -- you need to download the `zip` archive and view them in one of the various archived formats (`html`, `md` or `codeplexwiki`), all of which suffer from various broken links.

To the best of my knowledge, there isn't an "official" new home for Triangle.NET. If one is created, then I will add a link here (or transfer this repo over to him).

# Triangle.NET versions

There are 3 snapshot releases
([Beta 1](/releases/Triangle.NET%20Beta%201),
[Beta 2](/releases/Triangle.NET%20Beta%202)
and [Beta 3](/releases/Triangle.NET%20Beta%203))
and a current "Beta 4" version.

Note that there are significant differences between Beta 3 and Beta 4, which are discussed in the [From Beta 3 to 4](wiki/From_Beta_3_To_4.md) page.

The earlier releases are archived in the [releases](/releases) directory. The current Beta 4 release is in [source](/source).

# Documentation

This documention has been transferred from the wiki in the original Codeplex site (with links fixed, et al).

* [Home](wiki/Home.md)
* [Features](wiki/Features.md)
* [Documentation](wiki/Documentation.md)

Examples:

* [Example 2 - Creating a ring polygon](wiki/Example_2.md)
* [Example 4 - Refining regions](wiki/Example_4.md)
* [Example 5 - Finding boundary triangles](wiki/Example_5.md)
* [Example 6: Parallel mesh processing](wiki/Parallel_mesh_processing.md)
* [Example 7 - Boolean operations on meshes](wiki/Example_7.md)
* [Example 10 - Troubleshooting 1](wiki/Example_10.md)
* [Refining regions](wiki/Regions.md)
* [Using Quadratic Elements](wiki/Quadratic_Elements.md)

(No, I don't know why there are numbering gaps. The "missing" examples are most like elsewhere in the documentation.)

# Working with Unity

One common reason for forking the Triangle.NET code is to make the library compatible with Unity. The current (2017) Mono compiler used by Unity targets C# 4 and .NET 3.5, so any code in Triangle.NET that makes use of .NET 4.0 or later needs to have a workaround.

The upcoming 2018 release of Unity (currently at 2018.1 beta) will support C# 6 and .NET 4.7.1, so these workarounds shouldn't be necessary for much longer.

However, in the meantime...

To build a Unity-compatible DLL from your local `source/Triangle.NET/Triangle` directory, you can run:

```
mcs -target:library -out:triangle-net.dll -sdk:2 -define:UNITY -nowarn:414 -debug -recurse:*.cs
```

* `-sdk:2`: SDK version for referenced assemblies. Unity requires this to be 2 (options are 2, 4 and 4.5).
* `-define:UNITY`: enable workarounds in the code for .NET 4.0 stuff so that Unity is happy.
* `-nowarn:414`: suppress the warning about "TriangleNet.Meshing.Algorithm.Dwyer.rand" being assigned but never used.
* `-debug`: generate an `.mdb` file with debugging symbols.

A pre-built version of an `mcs`-compiled DLL (and symbols) suitable for use with Unity is located in [unity](unity).

The changes required to make Unity's runtime happy are in [this cl](https://github.com/garykac/triangle.net/commit/19bfd1b4eb79eb783511f69bc1593cf84261b256).
