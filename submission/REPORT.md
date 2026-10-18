# CrewAI Build Report

**Name:** Shreyansh Kuchanur
This topic is about how the roadster's battery and motor work.

## 1. What I changed

The voice of the crew was changed for a voice to a 10-year-old reader of the explainer. All of my edits are in `crewai-kit/main.py`. Instead of the default 'Technical Writer' agent, I used a new 'backstory' that made it write for curious 10-year-olds, with short sentences, comparisons that are in everyday use, and no jargon, and that explained any difficult word immediately. I also updated the `backstory` of the **Editor** agent to make it suitable to edit for 10-year-old readers and yet still indicate claims that require a source.

I also changed the Editor as well because its output is what will be saved to `output/explainer.md`, and it's what that gets saved as. Its job is to compress the language, and if it is only the Writer who had changed, the Editor could have pulled the language back towards that of the technical writer. I didn't alter the assignment, nor the Research Analyst or the topic, so the only thing that differentiates the two runs is the two backstories.

## 2. How CrewAI works

In CrewAI, an agent is a language model that is provided with a role, a goal and a story. The backstory is the description of the model that is sent to the model whenever that particular agent operates. A **task** is a piece of work which has a description, an expected output and an assigned agent. The crew carries out the activities of the agents and tasks, and executes them. This kit is sequential in nature; tasks are executed sequentially and the result of each task is provided to the next task as context.

In this case, the Research Analyst composes a brief that includes 5 key facts and 2 open questions. The Technical Writer reads that short summary and writes an explainer of 250 words.The Technical Writer reads that short summary and writes an explainer of 250 words. The Editor refines the explainer and tags any statements that require a source, resulting in a file that is saved in the file `output/explainer.md`.

The rationale behind my change being successful was the role descriptions remained constant, but the persona that the model was asked to assume had shifted. The Writer and Editor still performed the same duties, in the guise of a children's writer editor. The style of the brief changed only from the Writer onward, and the Research Analyst remained unaffected. The Editor added `[source needed]` because it is the instruction from the task I left it in.

## 3. Evidence

The explainer_run1.md (original) document resembles a technical article. It also references terms like "three-phase permanent-magnet synchronous motor", "low internal impedance", "I²R losses" and "charge-acceptance rate" without providing any definition.

Compared with the original text, the sentences in `explainer_run2.md` are short, and everyday comparisons are used. It equates two motors that power each wheel independently with a skateboard that can steer itself and the cooling loop with a garden hose with cool liquid and regenerative braking to a bike recharging its battery as it slows down. If it does use a hard term, such as alternating current, it explains it immediately afterwards, as the new backstory requested. The two versions still include `[source needed]` flags. The three agents run in the same sequence, and are the same on both runs shown in the terminal logs: run1_original.txt and run2_changed.txt.

Not all the difference is due to my change. The same content varies from one run to the next, where I did not edit that agent, but the Research Analyst did give different “facts” for these different runs (one motor used at approximately 400 V in Run 1, two motors used at up to 800 V in Run 2).

On an initial test of Run 1 and Run 2, the Technical Writer was not able to pass either run with the error "Invalid response from LLM call - None or empty". CrewAI tried again the Writer 3 times and finally he stopped the crew. I knew my API key and setup was correct since I had already worked on it. After waiting for around one minute I ran the same command again and it ran both times. Only kept successful runs.

## 4. Choosing a framework

I would choose CrewAI for a project like this, as the work is definitely divided into specific roles: one researcher, whose job is to collect information; a writer, whose job is to take data in and turn it into a story; and an editor, whose job is to take the story in and edit it. CrewAI allows me to write each role in simple words, and the tasks in sequential order, with each result sent to the next role. Only two backstories needed to be edited and there was no change to the logic of the program in order to change the audience of the entire crew.

It would have been more difficult for LangGraph to be configured for this straightforward pipeline. I would have to write each step and make it a node, connect the nodes with an edge and manage the state myself – which is still just a line of code. It would have been easier to manage my mistake if it had been LangGraph. When the Writer returned an empty response, CrewAI's retries were hidden inside the framework and the whole crew failed. In LangGraph I could include a conditional edge which would send the flow back to the writer node if the output from the writer node is empty, or a fact-check node which would send the flow back to the research node if it is not possible to support claims. CrewAI is faster for role based content pipelines, LangGraph allows for more control when the flow requires branches, loops, or custom error handling.

## 5. Verification and the use of AI techniques.

The kit does not provide any web access, and so the output of facts are from memory of the model and may be incorrect. Three approaches to checking the output.

First, I did a run against a comparison. You'll note that the Research Analyst provided conflicting facts on the 4680 cells, and 2170 cells, and one run detailed only a single motor running at approximately 400 V, while the other two motors were operating at up to 800 V. If it's a reliable source, it will not alter facts between runs.

Second, I looked at a number of claims in the Wikipedia articles of the Tesla Roadster. The original Roadster (2008) ran on approximately 6,800 small laptop lithium ion cells (53 kWh), and an AC induction motor which is not the type of output described. The 200 kWh pack and 620-mile range are kind of numbers that Tesla announced in 2017 for a next-generation Roadster (which is not EPA-tested) but the output calls the range "EPA-rated". Run 1 also says 2170 cells are about 46 mm wide, but "2170" means 21 mm × 70 mm.

Third, the Editor's `[source needed]` flags indicated those sentences in which there was no support. In summary, the changes are good for style, but the explainer cannot be relied upon as a fact, unless there are actual sources.

I used Claude (Anthropic's AI assistant) to help me with step-by-step instructions for the setup, running the kit and saving files on my Mac; to troubleshoot a "None or empty" error; for the new backstory text for the Writer and Editor; and to write this report, which I reviewed and edited. I ran the code and compared the outputs to ensure it's a code change and I checked facts as mentioned above.