# Liquids

Creating the liquid:
```java
package com.example.coppermod;
import net.minecraftforge.fluids.Fluid;

public class FluidGasoline extends Fluid {
    public FluidGasoline() {
        super("fluid_gasoline");
    }
}
```

Creating the liquid block:

```java
package com.example.coppermod;

import net.minecraft.block.material.Material;
import net.minecraft.client.renderer.texture.IIconRegister;
import net.minecraft.creativetab.CreativeTabs;
import net.minecraft.util.IIcon;
import net.minecraft.world.IBlockAccess;
import net.minecraftforge.fluids.BlockFluidClassic;

public class BlockGasoline extends BlockFluidClassic {

    private IIcon stillIcon;
    private IIcon flowIcon;

    public BlockGasoline() {
        super(CopperMod.fluidGasoline,Material.water);
        setBlockName("gasoline_block");
        setCreativeTab(CreativeTabs.tabMisc);

    }

    // prevent infinite fluid
    @Override
    public boolean isSourceBlock(IBlockAccess world, int x, int y, int z) {
        return false;
    }

    @Override
    public IIcon getIcon(int side, int meta) {
        return (side == 0 || side == 1) ? stillIcon : flowIcon;
    }

    @Override
    public void registerBlockIcons(IIconRegister register) {
        stillIcon = register.registerIcon(CopperMod.MODID + ":gasoline_still");
        flowIcon = register.registerIcon(CopperMod.MODID + ":gasoline_flow");
    }
}
```

Creating the liquid bucket:
```java
package com.example.coppermod;

import cpw.mods.fml.common.IFuelHandler;
import net.minecraft.block.Block;
import net.minecraft.creativetab.CreativeTabs;
import net.minecraft.item.ItemBucket;
import net.minecraft.item.ItemStack;

public class ItemGasolineBucket extends ItemBucket implements IFuelHandler {

    public ItemGasolineBucket(Block block) {
        super(block);
        this.setUnlocalizedName("gasoline_bucket");
        this.setTextureName("coppermod:gasoline_bucket");
        this.setCreativeTab(CreativeTabs.tabMisc);
    }

    @Override
    public int getBurnTime(ItemStack fuel) {
        return 20000; // Lava burn time
    }
}
```

Creating the event handler for filling our liquid into buckets:

```java
package com.example.coppermod;

import cpw.mods.fml.common.eventhandler.SubscribeEvent;
import net.minecraft.block.Block;
import net.minecraft.init.Blocks;
import net.minecraft.item.ItemStack;
import net.minecraftforge.event.entity.player.FillBucketEvent;

public class FillBucketEventHandler {
    @SubscribeEvent
    public void onFillBucketEvent(FillBucketEvent event){
        Block block = event.world.getBlock(event.target.blockX,event.target.blockY,event.target.blockZ);

        if(block.getClass() == BlockGasoline.class ){
            event.setCanceled(true);
            event.current.stackSize -= 1;
            event.entityPlayer.inventory.addItemStackToInventory(new ItemStack(CopperMod.gasolineBucket));
            event.world.setBlock(event.target.blockX,event.target.blockY,event.target.blockZ, Blocks.air);
        }
    }
}
```

Registering the liquid:

```java
fluidGasoline = new FluidGasoline();
FluidRegistry.registerFluid(fluidGasoline);

blockGasoline = new BlockGasoline();
GameRegistry.registerBlock(blockGasoline, MODID + "_" + blockGasoline.getUnlocalizedName());

gasolineBucket = new ItemGasolineBucket(blockGasoline);
GameRegistry.registerItem(gasolineBucket, MODID + "_" + gasolineBucket.getUnlocalizedName());

FluidContainerRegistry.registerFluidContainer(fluidGasoline,new ItemStack(gasolineBucket), new ItemStack(Items.bucket));
GameRegistry.registerFuelHandler(gasolineBucket);

FillBucketEventHandler fillBucketEventHandler = new FillBucketEventHandler();
MinecraftForge.EVENT_BUS.register(fillBucketEventHandler);
```

## Creating the liquid textures

Textures for fluids like water or lava are different in that they are animated. To create an animated water texture,
you can either edit the normal water/lava texture or create a texture with a size of 16x512 or 32x1024. Each 16x16 or
32x32 square represents an animation frame.

To get the texture animations working, you also need to create a .mcmeta file. If our texture name is
"gasoline_flow.png", then our animation file is named "gasoline_flow.png.mcmeta"

Here is a mcmeta file for the default animation speed.

```
{
  "animation": {}
}
```

If you want to slow down the animation, you could add the "frametime" attribute.

```
{
  "animation": {
    "frametime": 4
  }
}
```