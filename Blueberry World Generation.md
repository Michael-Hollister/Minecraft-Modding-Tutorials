# Generating blueberry crops

Generating blueberries in the world is a similar process to our Copper ore generation. To get started, we first need to
create a generator class.

```java
package com.example.coppermod;

import cpw.mods.fml.common.IWorldGenerator;
import net.minecraft.init.Blocks;
import net.minecraft.world.World;
import net.minecraft.world.chunk.IChunkProvider;

import java.util.Random;

public class BlueberryGenerator implements IWorldGenerator {
    /**
     * Generates blueberries in the world. Uses the Minecraft Mellon generation pattern
     *
     * @param random         the chunk specific {@link Random}.
     * @param chunkX         the chunk X coordinate of this chunk.
     * @param chunkZ         the chunk Z coordinate of this chunk.
     * @param world          : additionalData[0] The minecraft {@link World} we're generating for.
     * @param chunkGenerator : additionalData[1] The {@link IChunkProvider} that is generating.
     * @param chunkProvider  : additionalData[2] {@link IChunkProvider} that is requesting the world generation.
     */
    @Override
    public void generate(Random random, int chunkX, int chunkZ, World world, IChunkProvider chunkGenerator,
                         IChunkProvider chunkProvider) {
        // Generate blueberries only in the overworld. 0 is Overworld, -1 is Nether, and 1 is end.
        if(world.provider.dimensionId == 0){
            // The higher the frequency, the more likely blueberries will be generated.
            int frequency = 250;

            for (int l = 0; l < frequency; ++l)
            {
                // Pick a random x, y, z coordinate for this chunk and see if it is a valid location to place a block.
                int x = (chunkX * 16) + random.nextInt(16);
                int z = (chunkZ * 16) + random.nextInt(16);

                int height = world.getHeightValue(x, z) * 2;
                if (height < 1) height = 1;
                int y = random.nextInt(height) + random.nextInt(8);

                // Place blueberries only on top of grass blocks
                if (CopperMod.blueberryBlock.canPlaceBlockAt(world, x, y, z) &&
                        world.getBlock(x, y - 1, z) == Blocks.grass)
                {
                    world.setBlock(x, y, z, CopperMod.blueberryBlock, 0, 2);
                }
            }
        }
    }
}
```

Just like our with our Copper ore generator, we need to register it with the game. This should be added to the main mod
class (ExampleMod or CopperMod) with all of our other registering code.

```java
BlueberryGenerator blueberryGenerator = new BlueberryGenerator();
GameRegistry.registerWorldGenerator(blueberryGenerator, 0);
```

Lastly, since we are generating crops in the world, they have growth stages (8 for our crops). When the block is added
to the world, we need to tell minecraft which growth stage to place the block in the world with. The following method
can be added to our `BlockBlueberry` class.

```java
@Override
public void onBlockAdded(World world, int x, int y, int z) {
    int growthStage = 7;
    world.setBlockMetadataWithNotify(x, y, z, growthStage, 2);
}
```

Now your blueberry crops should generate in Minecraft.