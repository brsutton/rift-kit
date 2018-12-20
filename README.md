# Example Rift mod

This is a minimal Rift project. The only use of it is to be copied to start a new project. 

## Requirements:
- OpenJDK 8. Currently Minecraft doesn't support Java 9 and above.

## Gradle tasks
- Development:
    ```sh
    ./gradlew setupDecompWorkspace [eclipse,idea]
    ```
    After this, import in your IDE as Gradle project.

- Build:
    ```sh
    ./gradlew build
    ```

- Running Server under Eclipse:<br>
    Open *Launch Configurations*, select *yourmod*\_client, switch to *Arguments* tab, change program arguments from `...Client...` to `...Server...`:
    ```
    --tweakClass org.dimdev.riftloader.launch.RiftLoaderServerTweaker
    ```

## Getting started

Important files in MDK:

```sh - sh to highlight comments
src / main / java /      # code 

src / main / resources / 
        riftmod.json     # what classes to instantiate and call 
        pack.mcmeta      # resource pack description
        assets / modid / # blockstates/, sounds/, textures/, etc 

run /                    # dev env runs here; screenshots/, mods/, etc 

build /                  # build files 
        libs /           # compiled archives 

build.gradle             # how to build & dependencies 
```
<br>
From there, you'll probably want to change a few things:  

- `build.gradle`
    ```groovy
    /* rest omitted */
    group 'example' // your root package, e.g. 'org.dimdev.halflogs'
    version '1.0'
    archivesBaseName = 'Example'
    ```
    > Generally speaking, `group` should match Java package containing all project code, which by convention should start with owner's domain name, e.g. `org.dimdev.halflogs`.  

- `src/main/resources/riftmod.json`
    ```json
    {
        "id": "example",
        "name": "Example",
        "authors": [
            "ExampleAuthor"
        ],
        "listeners": [
            "example.ExampleListener"
        ]
    } 
    ```
    To load listener only on specific side or to change loading order:
    ```json
    {
        "listeners": [{
            "class": "example.ExampleListener",
	        "side": "client",
	        "priority": 10
        }]
    }
    ```
    Inner classes require `$` instead of `.`, e.g. `example.Example$InnerClass`. 

- `pack.mcmeta`
    ```json
    {
        "pack": {
            "pack_format": 4,
            "description": "Example"
        }
    }
    ```
    This description is shown at *Select Resource Packs* screen. If mod doesn't have any resources, you can safely delete this file.

## Snippets

- Changing `src/main/java` to `src` and `src/main/resources` to `resources`:
    ```gradle
    sourceSets {
        main {
            java.srcDirs = ['src']
	    resources.srcDirs = ['resources']
        }
    }
    ```

- Simple blocks and items:  
    ```java
    class SimpleThings implements BlockAdder, ItemAdder {
        public static final Block MYTHRIL_ORE;
        public static final Item MYTHRIL;
    
        static {
            Block.Properties mythrilOre = Block.Properties.create(Material.ROCK).hardnessAndResistance(5F, 15F);
            MYTHRIL_ORE = new Block(mythrilOre);
        
            MYTHRIL = new Item(new Item.Properties().maxStackSize(16)).group(ItemGroup.SEARCH);
        }
    
        @Override
	    public void registerBlocks() {
	    	Block.register(new ResourceLocation("example:mythril_ore"), MYTHRIL_ORE);
        }
    
        @Override
	    public void registerItems() {
		    Item.register(MYTHRIL_ORE, ItemGroup.SEARCH);
		    Item.register(new ResourceLocation("example:mythril"), MYTHRIL);
        }
    }
    ```
