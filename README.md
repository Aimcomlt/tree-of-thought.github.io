# tree-of-thought.github.io
Prompt engineering code examples.

///////////FIRST IS THE SUGGESTION AGENT WHO CREATE A KNOWLEDGE BASE WITH PERSONA FOR THE CHAT ROOMS:
import OpenAI from 'openai';

console.log(process.env.REACT_APP_OPENAI_API_KEY);

const openai = new OpenAI({
  
  apiKey: process.env.REACT_APP_OPENAI_API_KEY, 
  dangerouslyAllowBrowser: true
});

class SuggestionAgent {
  /**
   * Fetches agent suggestions based on the provided content and details.
   * @param {string} clientDetails - Additional details provided by the client.
   * @returns {Promise<Object|null>} - Returns agent suggestions or null in case of an error.
   */
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



export default SuggestionAgent;
