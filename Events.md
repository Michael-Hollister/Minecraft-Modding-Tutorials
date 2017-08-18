# Events

Many times in Minecraft we may want to execute our code when some action happens in the game. For example, maybe we want
to add a potion effect when the player wakes-up from sleeping, or maybe when an entity receives damage we want to print 
chat message.

In order to run our code when these actions happen, we use Forge's event bus. The event bus used for most events is 
`MinecraftForge.EVENT_BUS`.

Events work by registering an event handler with the desired event bus. An event handler is a class that contains at
least one void method marked with the `@SubscribeEvent` annotation.

The motivating example to demonstrate event registration will be to add blueberry seeds to drop when tall grass blocks
are broken by the player. Below is an example code that will drop blueberry seeds 50% of the time when the player breaks
tall grass.

```java
package com.example.coppermod;

import cpw.mods.fml.common.eventhandler.SubscribeEvent;
import net.minecraft.init.Blocks;
import net.minecraft.item.ItemStack;
import net.minecraftforge.event.world.BlockEvent;

public class BlockBreakEventHandler {
    @SubscribeEvent
    public void onBlockBreakEvent(BlockEvent.HarvestDropsEvent event){
        if(event.block == Blocks.tallgrass){
            // We will drop our blueberry seeds 50% of the time
            if(Math.random() > 0.5){
                event.drops.add(new ItemStack(CopperMod.blueberry));
            }
        }
    }
}
```

Whenever we create a method that handles an event, we need to put the `@SubscribeEvent` annotation above our method
declaration. To handle a specific event, we create this method to have one parameter with its type corresponding
to the event we wish to handle. In the above case, it is the `BlockEvent.HarvestDropsEvent` type.

Finally, register the event in the `init` method our `CopperMod` class.

```java
BlockBreakEventHandler blockBreakEventHandler = new BlockBreakEventHandler();
MinecraftForge.EVENT_BUS.register(blockBreakEventHandler);
```

Now try experimenting with other events! To find the available events, look at your project window in IntelliJ and
expand External Libraries -> forgeSrc-1.7.10-******.jar -> net -> minecraftforge -> event to find the available events.

Here is another example which adds potion effects after sleeping.

```java
package com.example.coppermod;

import cpw.mods.fml.common.eventhandler.SubscribeEvent;
import net.minecraft.potion.PotionEffect;
import net.minecraftforge.event.entity.player.PlayerWakeUpEvent;

public class PlayerWakeUpEventHandler {
    @SubscribeEvent
    public void onPlayerWakeUpEvent(PlayerWakeUpEvent event){
        event.entityPlayer.addPotionEffect(new PotionEffect(1,1200,2));
    }
}
```


