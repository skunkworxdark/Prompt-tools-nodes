# Prompt-tools-nodes
A set of InvokeAI nodes that add general prompt (string) manipulation tools.  These where written to accompany the PromptsFromFile node and other prompt generation nodes.

|Node|Description|
|---|---|
|*`Prompt Join`|Joins two prompts into one.|
|*`Prompt Join Three`|Joins 3 prompt together.|
|*`Prompt Replace`|Performs a search and replace on a prompt. With the option of using regex.|
|*`Prompt Split Neg`|Splits a prompt into positive and negative using the old V2 method of [] for negative.|
|`Prompt To File`|saves a prompt or collection of prompts to a file. one per line. There is an append/overwrite option.|
|`PT Fields Collect`|Converts image generation fields into a Json format string that can be passed to Prompt to file.|
|`PT Fields Expand`|Takes Json string and converts it to individual generation parameters This can be fed from the Prompts to file node|
|`Prompt Strength`|Takes a string and float and outputs another string in the format of (string)strength like the weighted format of compel. |
|`Prompt Strength Combine`|This takes a collection of prompt strength strings and outputs a string in the .and() or .blend() format that can be fed into a proper prompt node.|

***=** The following Nodes are due for inclusion into a future version of Invoke (as part of [PR-#3964](https://github.com/invoke-ai/InvokeAI/pull/3964)) and at that time they will be removed from this set of tools as they will be redundant.  However as the nodes will be renamed in core there will be no classes if you decide to keep them.<br>
`Prompt Join` will become `String Join`<br>
`Prompt Join Three` will become `String Join Three`<br>
`Prompt Replace` will become `String Replace`<br>
`Prompt Split Neg` will become `String Split Neg`<br>
Additionally there will be a New generic `String Split` node<br>

## Usage
If you want to use these nodes then you will have to place the [prompt_tools.py](prompt_tools.py) file in the folder `".venv\Lib\site-packages\invokeai\app\invocations"`. 

## Discord Link
The discord link for discussion is :- [Prompt-Tools](https://discord.com/channels/1020123559063990373/1134084151386058803) .

## ToDo
- Test the new PTFields nodes
- Add validation to nodes generally.
- workout how to add other fields like schedules, vae, models etc.


## PromptNegSplit 
`PromptNegSplit` - This would take input in the old 2.3.5 format "Positive [Negative]" and split it into two separate prompts everything inside [] would go to the neg prompt and everything else would go to the positive.  This would allow the PromptsFromFile to contain both positive and negative prompts on a single line. This is the Node name I like the least as it really doesn't split in the normal programming sense its more of an extract but split is an easy concept to get for users.

![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/847f0a96-953d-4dc7-b6e2-a7417f106b99)


## PromptJoin and PromptJoinThree
`PromptJoin` - This would take two prompts and join them into one. The use case I have in mind is that you could add to the beginning or end of individual split prompts.  There are many more use cases for such a simple node. I thought about calling it PromptConcatenate but feel it is a bit to programming orientated.
![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/2a4957c7-e703-444f-b1bd-15c1b91393a7)

`PromptJoinThree` - Joins 3 prompts together. Useful for surrounding things
![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/d4fa05e1-ef18-44e2-88fd-bbc0ae134e4e)


## PromptReplace - Details and example
This takes 4 inputs (a prompt, a search string,  a replace string and bool for Regex).  As default the search is case insensitive but there is an option to switch to RegEx for more control on searches.

This can be used to just replace or remove terms from prompts. But due to its simplicity there are many many use cases. but comes into its own with nodes that either generate prompts or load them from other sources.

One such use case I have in mind is the PromptsFromFile would contain multiple template like prompts (e.g "photo of XXXXXX , Nikon", "drawing of XXXXXX in crayon", "Wikihow of XXXXXX, in a cartoon style") with a fixed place holder ( "XXXXXX") that would be replaced for each line of the file with whatever you put tin the replace field. This way you could change the subject of each prompt and try it against multiple templates.

![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/3479f673-09c5-4053-a8c9-e9e4ff4bc42e)


## PromptsToFile
`PromptsToFile` - Takes a filepath and a prompt or prompt collection and a bool for append/overwrite toggle.

Filepath:  This is the file that you want to be written to.  if the does not exist it will be created. Please be careful as there is no warning if a file already exists and will be overwritten.

Prompts: This accepts either a single prompt or a collection of prompts as input. 

Append: The defaults to append mode so the new prompts will just be appended to the end of the existing file. if this is disabled a new blank file will be created each time it is called. 

The large node graph below shows how you can use the Collect node make a collection of prompts during an iterate process.  This node graph goes a step further and places the negative inside [] like the old v2 way of doing things but has the benefit of keeping it to one line per prompt.

![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/b483a0e9-bd98-44ef-8c0e-0dc1b884deee)
![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/db82f094-ace7-4450-a418-31af64c01724)


## PTFieldsCollect and PTFieldsExpand
Note: Both of the PTfields nodes should be easy to change to add or remove fields if the ones provided are not correct.

`PTFieldsCollect` - Converts image generation fields into a Json format string that can be passed to Prompt to file
![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/3a716fe3-5e7d-41dd-80a2-3055cb4e7daf)

`PTFieldsExpand` - Takes a json string and converts it to individual generation parameters
![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/f0d733c1-74f4-4b92-b0c1-a813e7106530)


## PromptStrength and PromptStrengthsCombine
`PromptStrength` - converts a prompt and strength into compel style weighted string. Multiples of these then can be fed into a collect node to create a collection of them and the used with the 'PromptStrengthsCombine` node
```
e.g.
prompt: A blues sphere
strength: 1.2
output: (A blue sphere)1.2
```

`PromptStrengthsCombine` - converts a collection of PromptStrengths into a .and or .blend style string
```
e.g.
input: ["(cow)0.5","(Field)1.2","(stream)0.5"]
output: ("cow","Field","stream").and(0.5,1.2,0.5)
```

![image](https://github.com/skunkworxdark/Prompt-tools-nodes/assets/21961335/ce9120dd-b3fa-470e-ac29-b9acfb6e240f)

