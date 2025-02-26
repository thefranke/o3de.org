---
description: >-
  The Open 3D Engine Asset Bundler is a tool that you use to package and
  deploy the actual assets that are used in your game. This tool also provides timelier
  and more efficient content updates and patches.
title: What is the Open 3D Engine Asset Bundler?
weight: 100
---

The Asset Bundler is a command-line tool, `AssetBundlerBatch.exe`, and a set of specific file formats generated and used by the tool.

## Prerequisites to use the Asset Bundler 

To use the Asset Bundler, your game project must meet the following criteria:
+ The assets that you are bundling have been processed by the [Asset Processor](/docs/user-guide/assets/pipeline/processor/).
+ You have Visual Studio 2019 (any edition) installed and configured for C++ development.

## Why use the Asset Bundler? 

With the Asset Bundler, you don't have to figure out which assets that you need to include in your game and which assets to exclude. This can help save you time. For example, you have a working folder with 10,000 assets, but your final game uses only 5,000 of those assets. You want to ship only those 5,000 assets. Tracking this manually can quickly become complicated and expensive.

The O3DE Asset Bundlerhelps make shipping the specific assets used for the release of your game more reliable and repeatable. The reliability is based on an underlying dependency system that informs Asset Bundler which assets to include. If you make changes to your game and add or remove assets, the next time that you run the Asset Bundler, it automatically includes or excludes these new assets for you. The repeatability is based on underlying configuration files that provides consistency across different runs of the Asset Bundler.

By using the Asset Bundler, because you have smaller release packages, less risk of shipping unused or inappropriate assets, and more effective asset management.

## How do I use the Asset Bundler? 

Generating a platform-specific asset bundle using the Asset Bundler follows these steps:

1. **Define your seeds**. A *seed* is generally a top-level asset, such as a `.pak` file that contains an entire game or game level. You must have one or more seeds defined in a seed list. A seed list is a file with the suffix `.seed`.

1. **Generate your asset lists**. The Asset Bundler evaluates each seed defined in your seed list. It then recursively evaluates each dependent asset for inclusion in a complete asset list. Generated asset lists have the suffix `.assetlist`.

1. **Configure your bundle settings**. Provide a configuration file, called the "bundle settings file," which has the suffix: `.bundlesettings`. It contains all of the specific settings, such as the maximum bundle size, when creating a release bundle for your game.

1. **Bundle your assets**. The Asset Bundler utility,` AssetBundlerBatch.exe`, uses the asset list and bundle settings files to create a release bundle as a `.pak` file.

   The following diagram illustrates the relationships and process for bundling assets with the Asset Bundler.

![\[The steps used in the general process for bundling assets with O3DE.\]](/images/user-guide/assetbundler/asset-bundler-overview.png)

In this example, the seeds are the assets Level1.pak and Level2.pak. These `.pak` files reference O3DE slice files, which reference the entity meshes they contain, which in turn reference the material and texture files for those entities.

With those product dependency relationships in place, the Asset Bundler examines the hierarchies of each seed and generates an asset list. Asset lists, along with the bundle settings file that you create, are used to assemble the final bundle as a `.pak` file with all of the dependent assets. Any assets not associated with a seed are not included in the final release bundle.

The Asset Bundler runs whenever the Asset Processor starts, which includes any time you launch O3DE. You can also run it from the command line with the `AssetBundlerBatch` command. For more information on the latter, read the [Asset Bundler Command Line Reference](/docs/user-guide/packaging/asset-bundler/command-line-reference).


## Why define product dependencies? 

Many game projects restrict the content in their bundled builds. This is often done to hit target build sizes, such as the over-the-air download cap on iOS. It can also restrict content from builds that you really don't want in your release, such as content that would result in a negative ESRB rating for your game. The product dependency system provides tools to help you understand why assets are included in your asset bundles. This lets you debug the associated references and remove them if you don't want an asset.

There are two types of asset dependencies:
+ *Product dependencies*. This type of dependency is between two product assets. It declares that there is a specific link between these two assets, and in most cases you'll want both available in your final packaged build of your game.
+ *Source dependencies*. This type of dependency indicates that a source asset must be processed first, and that information from processing the source asset is required before the final product asset can be generated. For example, a process in an asset pipeline that runs on `Tree.fbx` has declared a source dependency on `Tree.mtl`. The `Tree.fbx` job won't run until the `Tree.mtl` job has completed, which guarantees that product details for `Tree.mtl` are available when the `Tree.fbx` job runs, and that information can be used in the generation of `Tree.fbx`'s product \(in this example, a single CGF file, `Tree.cgf`\).

In these topics, we are referring to *product dependencies* unless otherwise indicated.

The Asset Bundler also lets you exclude content that you don't want to release using the [asset list comparison feature](/docs/user-guide/packaging/asset-bundler/list-operations).

## How product asset dependencies are used by O3DE 

Product dependencies generate asset lists from seed lists. This simplifies the management of what goes into your packaged game content for your game releases. These dependencies are expressed as paths \(either relative or absolute, and can optionally include the wildcard `*`\) and are managed by an Asset Builder during processing. Within the Asset Builder, the `m_pathDependencies` variable contains the list of dependency paths.

For example, you have a game that has one level for your release build. Your game contains a single tree. You also have a separate test level that you don't need to ship, which contains a single rock.

![\[Screenshot of a set of dependent assets represented as a folder and file structure on a disk.\]](/images/user-guide/assetbundler/asset-bundler-simple-game-files.png)

The following example shows a basic workflow for a simple game with assets that emits dependencies correctly.

![\[New Asset Bundler dependency workflow.\]](/images/user-guide/assetbundler/new-asset-bundler-flowchart-simple.png)

For more complex game projects, with file loads implemented in C++ and custom asset builders, the asset bundling workflow can look more like this:

![\[New Asset Bundler workflow for complex projects.\]](/images/user-guide/assetbundler/new-asset-bundler-flowchart.png)

With the O3DE asset bundling workflow, you need only dig into the individual asset systems when a problem occurs using [the missing asset scanning tools](/docs/user-guide/packaging/asset-bundler/assets-resolving/). You also have opportunities early in development to verify that you're bundling the correct assets.

## Asset Builders and the Asset Bundler 

An Asset Builder is a standalone application that primarily translates intermediate assets to a platform-native asset format. An Asset Builder also provides dependency tracking and tasks related to managing asset metadata.

O3DE ships with Asset Builders for many common asset types. The Asset Bundler relies on the information produced by the Asset Builders for your project to manage the dependencies. If you use custom asset types, you can [create your own Asset Builders](/docs/learning-guide/tutorials/assets/custom-builder/) to support proper asset management and bundling with O3DE.

For example, Asset Builders for images can convert any portable image asset into a set of performance image formats. When the Asset Builder for the images run, they create the dependency tree for the performance-oriented image formats generated from the more general-use portable image formats, and define them as dependent on the respective portable image format. If you create the performance-oriented images manually or through a separate process, the dependencies are not defined and not available to the Asset Bundler.

In addition to processing your asset, Asset Builders also determine any product or source asset dependencies and store that information in an Asset Catalog for later use by the Asset Bundler. Specifically, "defining asset dependencies" means updating your custom Asset Builder to identify all of the other assets that the asset being processed depends on. It is important to define asset dependencies so that you can perform accurate Asset Bundling to ship your game. Without defined dependencies the Asset Bundler has no way to know which assets your game needs when it's time to prepare your asset bundles release-you could end up missing assets, or include too many, or ship undesirable ones. With asset dependencies defined in your Asset Builder(s), you can be assured that you are including exactly the assets you need for your game and nothing more.

You can use the Asset Builder APIs to develop your own Asset Builders, which can then process your custom asset type's source files and generate files that you can use in O3DE. Source code for a sample Asset Builder implementation is provided in the `Games/CustomAssetExample/Code/Source/Builder` directory under your O3DE installation root.

For more information, see the [the Asset Builder documentation](/docs/learning-guide/tutorials/assets/custom-builder/).

## Define dependencies in your Asset Builder 

In addition to processing your asset, Asset Builders also define any product or source asset dependencies, and then store that information in a database for later use by the Asset Bundler. Specifically, defining an asset dependency means updating your custom Asset Builder to identify all of the other assets that the asset being processed depends on. It is important to define asset dependencies so that you can perform accurate Asset Bundling to ship your game. Without defined dependencies, the Asset Bundler cannot identify which assets your game needs when it's time to prepare your asset bundles release. As a result, you could end up missing assets, including too many, or shipping undesirable ones. With Asset Dependencies defined in your Asset Builders, you are including the exact assets that you need for your game.

## Asset bundling and source control 

During the asset bundling process, you can generate multiple different types of artifacts. We recommend that you track some of these in source control, as per the suggested best practices in the following table.


**Asset bundling artifact storage best practices**

| File Extension | Source Control Details | File Storage Best Practices |
| --- | --- | --- |
| .seed | Keep your seed lists in source control. |  If you have multiple builds and are patching bugs in older revisions of your game, consider keeping versioned seed lists that match each release. For example, you could keep seed lists for multiple releases on Steam, such as an unstable feature release and your stable release. If your game has one release for a specific platform and your release pipeline is relatively uncomplicated, you can maintain one version of a seed list. You can also track your game's seeds by splitting the contents into multiple seed lists. The Asset Bundler can combine files at different steps in the process, so you don't need to make sure the files used in earlier steps look like your final output.  |
| .assetlist | Keep your asset lists in source control. This makes it more efficient to generate delta patches for your game. These are patches that containonly modified files since your last release. | You should store a separate asset list file in your source control for each release that you generate from them. Asset lists contain fingerprint information for each file. If you want to generate a delta patch and you don't have your asset list from the previous version of your game, you must sync your source control back to the exact time you generated that first asset list. Storing each asset list for a version separately can save you time later. For more information, see [Asset Bundling & Release Build Tutorial - Delta Bundles, Patching Content](https://wiki.agscollab.com/pages/viewpage.action?pageId=81283760). |
| .comparisonrules | If you use comparison rules, keep them in source control. | Storing your comparison rules in a file is an optional step in the asset bundling workflow. Use comparison rule files to help make sure that you're generating your packaged game content in the same way across multiple releases. |
| .bundlesettings | If you use bundle settings, keep them in source control. | Storing your bundle settings in a file is an optional step in the asset bundling workflow. Use bundle settings files to help make sure that you're generating your packaged game content in the same way across multiple releases. |
| .pak (asset bundles) | Don't store .pak files for your asset bundles in standard source control. Keep them in a separate secured file store. | Keeping track of your previously released content can help you debug problems. Asset bundles can be large, so diffing them isn't particularly useful. We recommend that you find an alternate place to store these files. If you're using Git as your source control solution, Git LFS can provide a useful home for these files. You can also use Amazon S3 to store your historical asset bundles. |

In the following example, there is a game that has two sets of bundled content: Assets loaded by the engine itself, and assets loaded by the game. In this scenario, the game has just release a version 2 update. Bundles have been created that upgrade existing version 1 players to version 2; amd version 2 only bundles content for new players who don't have version 1 content.

The following table provides some examples of the artifacts generated and used in bundling for this example scenario and how they are used.


**Example file artifacts in an Asset Bundler workflow**

| Files (Example) | Usage | Notes |
| --- | --- | --- |
|    | The asset lists used to generate the bundles for version 1 of the game. | You should keep these files in source control with the version in the file name. This lets you use these files to generate delta patches for future game releases. |
|    | Asset lists to generate bundles for version 2 of the game. | You should keep these files in source control with the version in the file name. This allows these files to be easily used to generate delta patches for future game releases. |
|    | These are asset lists generated as an interim step in creating the game bundle. An asset list comparison that removes content in the engine asset list is used to trim these down to the "engineRemoved" asset lists in the previous row. | You may have asset lists that you generate as interim steps that aren't used to create bundles directly. These could go into source control as a historical record, but they won't be used in the future to generate delta patches. |
|    | The asset list comparison rules. One set of rules is used to generate the upgrade bundles between versions. The other set of rules is used to remove assets in the engine asset list from the game asset list. | Comparison rules files should be stored in source control, so you can use the same bundle creation process each release. Versioning information is not stored in the file name, because it's unlikely you will need to use two versions at the same time. If you ever do, you can rely on your source control's history to retrieve the older version. |
|    | The settings used to generate the bundles. | Bundle settings files should be stored in source control, so you can use the same bundle creation process each release. |
|    | The game's seed list. | Your game's seed list should be stored in source control. As you change your game's content, you'll add and remove seeds from it. Versioning information is not stored in the file name, because it's unlikely you will need to use two versions at the same time. If you do, rely on your source control's history to retrieve older versions. |
|    | The asset bundles generated for each release. | Keep track of your game content's packaged asset bundles, to use in your game's releases. You can store your game's packaged content in source control, but this is not optimal, because these are large, binary files. We recommend that you use a separate storage solution, like Amazon S3. |

## Additional Resources 
+ [Asset Bundler Concepts and Terms](/docs/user-guide/packaging/asset-bundler/concepts)
