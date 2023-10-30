# tree-of-thought.github.io
Prompt engineering code examples.

///////////FIRST IS THE SUGGESTION AGENT WHO CREATE A KNOWLEDGE BASE WITH PERSONA FOR THE CHAT ROOMS:

class SuggestionAgent {

  async getSuggestions(clientDetails) {
   try {
          // Transforming clientDetails into a Generated Knowledge Prompting structure
          const generatedKnowledgePrompting = {
            prompt: {
              instruction: "Following the Generate Knowledge Prompting methodology, take into account the stages of Knowledge Generation, Knowledge Integration, and Prompt guidelines. Design a specialized agent considering the client's specific requirements, ensuring that the design involves a cohesive integration of various knowledge bases to produce the desired answer.",
              demonstrations: [
                {
                  question: `Construct an agent name, system prompt, user prompt and assistant prompt primarily focus on these details: ${clientDetails}`,
                  generatedContent: {
                    AgentName: "ExampleName",
                    SystemPrompt: "ExampleSystemPrompt",
                    UserPrompt: "ExampleUserPrompt",
                    AssistantPrompt: "ExampleAssistantPrompt"
                  }
                }
              ],
              question: `Generate the content for "AgentName", "SystemPrompt", "UserPrompt", and "AssistantPrompt" from this details: ${clientDetails}?`
            }
          };
    
          const chatCompletion = await openai.chat.completions.create({
            model: "gpt-3.5-turbo",
            messages: [
              {
                role: "system",
                content: `You are tasked with designing a specialized agent using the Generated Knowledge Prompting approach. Here's your instruction: ${generatedKnowledgePrompting.prompt.instruction}. Base your responses on the provided demonstrations: ${JSON.stringify(generatedKnowledgePrompting.prompt.demonstrations[0])}. Now, answer the following question: ${generatedKnowledgePrompting.prompt.question}.`,
              },
              {
                role: "user",
                content: `Always generate a schema and the content according to the system and the assistant in json format starting with: "AgentName": , "SystemPrompt": , "UserPrompt":  and "AssistantPrompt": .`,
              },
              {
                role: "assistant",
                content: `Understood. Using the Generate Knowledge Prompting method, I'll systematically navigate through the prompt, demonstrations, and question to derive the appropriate agent attributes tailored to the client's requirements. The result will be presented in a structured JSON schema as specified.`,
              },
            ],
          });

      const response = chatCompletion.choices[0].message.content;
      console.log('***: ', response);
      const myHandle = JSON.parse(response);
      console.log('************* : ', myHandle.AgentName, myHandle.SystemPrompt, myHandle.UserPrompt, myHandle.AssistantPrompt);
      return {
        agentName: myHandle.AgentName,
        systemPrompt: myHandle.SystemPrompt,
        userPrompt: myHandle.UserPrompt,
        assistantPrompt: myHandle.AssistantPrompt,
      };

    } catch (error) {
      console.error('Error with OpenAI chat completion:', error);
      return null;
    }
  }
}
////////////////NEXT IS THE AGENT CHAT ROOM FOR THE TREE OF THOUGHT METHODOLOGY:
/**
 * This function is responsible for generating the agent's response.
 * It uses the OpenAI API to get a response based on the agent's details and the user's input.
 */
async function MultiAgentFunctionGenerater(agent, userInput) {
  // Extracting agent details and user input
  const AGENT_NAME = agent.agentName;
  const SYSTEM_PROMPT = JSON.stringify(agent.systemPrompt);
  const USER_PROMPT = JSON.stringify(userInput); //agent.userPrompt
  const ASSISTANT_PROMPT = JSON.stringify(agent.assistantPrompt);
   console.log('THE AGENT: ', AGENT_NAME, SYSTEM_PROMPT, USER_PROMPT, ASSISTANT_PROMPT );
 
  // Get the agent's response using the createChatCompletion function
  try {
 
    const chatCompletion = await openai.chat.completions.create({
      model: "gpt-3.5-turbo",

      messages: [
        { role: "system", content: `After clarifying the client details from here: ${USER_PROMPT} and pondered the system prompt here: ${SYSTEM_PROMPT}. 
        Imagine three different experts that work in the same fields as describe in the System Prompt: ${SYSTEM_PROMPT}. Now as three specialized agents, there task will be to generate responses to the question below.
        The experts interactions will include brainstorming, evaluating, critiquing, fact-checking, and reaching a consensus step by step reasoning with the client details here: ${USER_PROMPT} carefully taking into account there mandate describe in the System Prompt.
        All specialized agents including agent AI ${AGENT_NAME} will write down 1 step of their thinking,
        then share it with the group.
        They will each critique their response, and all the responses of others
        They will check their response based on common knowledge of there trainning.
        Then all experts will go on to the next step and share with the assistant this step of their thinking.
        They will keep going through steps until they reach their consensus taking into account the thoughts of the other specialized agents
        If at any time they realise that there is a flaw in their logic they will backtrack to where that flaw occurred 
        If any agents realises they're wrong at any point then they acknowledges this and start another train of thought
        Each expert will assign a value between 1 and 10 were 10 is the assertion of being most correct.
        Continue until the specialized agents assigned value is greater then 0.89 or agree on the single most likely response to generate. 
        The reponse must be shared with the assistant and with agent AI ${AGENT_NAME}.
        ${AGENT_NAME} become a highly knowlegable AI Agent.
        The question is: ${AGENT_NAME} has pondered the system prompt as if it was he's own brain's blueprint. 
                         While pondering the system blueprint, ${AGENT_NAME} is task to chat and generate content base on the highest value between 0 and 1 were 1 is the assertion of being the most correct of the three experts  
                         ${AGENT_NAME} having taken the time to understand the experts response. What is ${AGENT_NAME} response?`},
                         
        { role: "user", content: `You are agent AI ${AGENT_NAME} you have the knowledge of the three experts to generate a clear and consist response. Generate response and add the resulting value between 0 and 1 of your response.`},
        {role: "assistant", content: `Based on the detailed brainstorming and the specialized agents opinions, I'm tasked to generate a response. 
        If the content revolves around coding in any language, I will block code it in any language. 
        If it's a book plot, I'll present it in paragraph form. 
        If it's a review, I'll use bullet points. 
        And if the data is best presented in a table, I'll use a tabular format. 
        Now, taking into account all the information and the mandate, here's my response:`}

      ],
      
    });

    let GeneratedResponse = chatCompletion.choices[0].message["content"];
    if (GeneratedResponse) {
      console.log('TREE OF THOUGHT MYTHODOLOGY 1.: ', GeneratedResponse)
      //fs.writeFileSync(generatedResponseFilePath, JSON.stringify(GeneratedResponse, null, 2));
      return GeneratedResponse;
    } else {
      throw new Error("GeneratedResponse exceeds the token limit.");
    }
  } catch (err) {
    console.error(err);
    throw err;
  }
}
