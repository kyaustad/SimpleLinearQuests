# Quick Start

This page should get you up and running and creating a linear story-line in 5m!

## Add Actor Component

Once the asset pack is added to your existing project, go to your character pawn and add the *AC_PlayerQuestManager*.

![](https://github.com/kyaustad/SimpleLinearQuests/raw/refs/heads/main/Media/Docs/1.png)

### Initialize the component

The actor component needs to be initialized with your starting quest. The easiest way to do this is to drag the *AC_PlayerQuestManager* you just added from the components list on the left, into your blueprint graph to get a reference to it. From there drag off the component reference and call the function *Initialize Quest Component*. Connect this to the *BeginPlay* event.

![](https://github.com/kyaustad/SimpleLinearQuests/raw/refs/heads/main/Media/Docs/2.png)

The input *Start Quest* can be filled in with your starting quest once you have a data table for your quests.

## Creating Quest Data-Table

Next, you can either edit the existing data table that comes in the demo files or create your own data table for your quests based on the struct included in the kit titled *S_Quests*.

Once you have a data table created, each row is a unique quest. In the *Row Name* field, give your quest a unique name. If you plan on integrating with some form of UI for the player to see what quest they are on I recommend using and underscore for spaces. In the demo HUD, a *Replace* string node is used to replace underscores with spaces so the name is displayed in a proper manner to anything visible to the player. If you plan on using the system simply for internal tracking of player progress and they won't ever see the quest names, it doesn't matter. Go through and add your various variables for your quest. 

![](https://github.com/kyaustad/SimpleLinearQuests/raw/refs/heads/main/Media/Docs/3.png)

* Quest Description: A text description of your quest.
* Previous Quest: The quest before this one. Used to error check progress and make sure completed quests are added to the completed quests.
* Objectives: An array of *S_Objectives* structs that contain 3 variables for each objective: The *ObjectiveName* is used to identify the objective. It is case sensitive but can be anything you wish to identify the objective internally as well as display the current objective to the player. *ObjectiveQuantity* is used to tell how many of that objective must be completed to complete the objective (Such as collect 3 cubes). And *ObjectiveDescription* is used to display to the player a description of their current objective.
* NextQuest: This is used when the current quest is completed to assign the next quest to the player. Make sure it matches the *Row Name* of the quest you want to assign next exactly.
* QuestIcon: Used when integrating with the hud to display an icon specific to the quest.

### Assign the quest data-table

Back in your character pawn, select the quest manager component and find the variable in the details panel called *Quests Data Table* and assign the table you just created/edited to that variable. Also input your starting quest in the *Initialize Quest Component* function.

![](https://github.com/kyaustad/SimpleLinearQuests/raw/refs/heads/main/Media/Docs/5.png)

## Creating Objectives

Lastly, you need something for the player to actually do to progress their objectives. Included in the demo files is a *BP_BaseItem*. It contains two variables so for the quick start guide I will show how to use this item but the functionality can be used at any time by any actor.

![](https://github.com/kyaustad/SimpleLinearQuests/raw/refs/heads/main/Media/Docs/6.png)

If you are implementing this with your own item or NPC simply create these 2 variables. Really you only need the 'QuestsAndObjectives' variable if you aren't deleting the object when it updates an objective. *QuestsAndObjectives* variable is an array of *S_Objectives* structs where you can define the corresponding objective names and how much to update their progress by in the *ObjectiveQuantity* field. The *ObjectiveDescription* field of the struct is unused here.

Drag an instance of *BP_BaseItem* into your world and under the details panel add items to the *QuestsAndObjectives* array to correspond to the objectives you want to update. If you want that instance of *BP_BaseItem* to delete when it updates an objective or not, set *ShouldDeleteOnObjectiveUpdate?* accordingly. True means it will delete.

Now walking launch the game and when your character pawn collides with the instances of *BP_BaseItem* it will check if any of its assigned objective names match the current quests current objective and update accordingly. If you plan to implement the quest updating on a different actor, say during an existing dialogue system interaction or upon pulling a lever, all you have to do is on that different actor have an array of *S_Objectives* structs and assign your objectives to update in there. Then, when you wish to trigger the objective update call two functions: the global function *Get Player Quest Component* and from the returned component from that function, call *Update Current Quest*. On that function pass in your *QuestsAndObjectives* variable to the function and if you wish the triggering actor to be destroyed, pass in a *self* reference into the *Interacted Actor* input on that function.

![](https://github.com/kyaustad/SimpleLinearQuests/raw/refs/heads/main/Media/Docs/7.png)

That's it! Passing an array of objective structs (which can contain only 1 objective if you only need that actor to update a single objective) into the *Update Current Quest* function will update the quests according to the flow and structure set in your *Quests Data Table* assigned on the Player Quest Manager.
