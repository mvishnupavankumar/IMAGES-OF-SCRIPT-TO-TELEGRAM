# IMAGES-OF-SCRIPT-TO-TELEGRAM

 Daily Anime Scene Generator to Telegram using n8n
📖 Description
This n8n workflow is designed for anyone who loves anime-style visual stories and wants to receive a fresh, emotionally intense 60-second animated visual story daily, broken into six interconnected scenes, directly on Telegram!

For tech-savvy users, this automation leverages the OpenRouter API to generate the script for the visual story using the Gemini-2.5-flash-lite-preview-06-17 model, then utilizes the Together API (specifically the black-forest-labs/FLUX.1-schnell model) to generate an image for each scene based on the script, and finally sends these images with their corresponding scene captions to a Telegram chat. It's a powerful integration for automated content creation and delivery.

⚙️ How It Works (step-by-step with emojis)
This workflow runs on a schedule, generates a visual story script and corresponding images, and then sends them to your Telegram:

🔹 1. Schedule Trigger ⏰
Simple: This node sets the workflow to run automatically every day at 9 AM.

Tech: Uses the Schedule Trigger node with a cron configuration to initiate the workflow daily.

🔹 2. SCRIPT 📝
Simple: This is where the magic of storytelling begins! It sends a detailed prompt to an AI model to generate a 60-second anime-style visual story script, broken into six scenes.

Tech: An HTTP Request node makes a POST request to the OpenRouter API (https://openrouter.ai/api/v1/chat/completions) with application/json content type and Bearer token authentication. The JSON body specifies the google/gemini-2.5-flash-lite-preview-06-17 model and a comprehensive role prompt for generating visual stories with specific emotional and stylistic requirements.

🔹 3. SENDING SCRIPT 📨
Simple: This node takes the generated story script and prepares it for further processing.

Tech: A Set node extracts the content of the AI-generated script from the previous HTTP Request node's response ($json.body.choices[0].message.content) and assigns it to a variable named SCRIPT.

🔹 4. CLEAN SCRIPT 🧼
Simple: This node tidies up the script, separating each scene into its own distinct piece of data.

Tech: A Code node uses JavaScript to parse the SCRIPT variable. It uses a regular expression to find and extract each "SCENE" block, creating separate JSON items for each scene with its title and content.

🔹 5. COMBINE SCENE 🔗
Simple: This step formats each individual scene into a readable message.

Tech: Another Code node iterates through the output of the "CLEAN SCRIPT" node, combining the scene title and content into a single message string, prefixed with an emoji.

🔹 6. EXTRACT SCENE 📦
Simple: This node grabs the combined scene messages to prepare them for further separation.

Tech: A Set node takes the message from the "COMBINE SCENE" node and assigns it to a new variable called scenesList.

🔹 7. SEPERATE SCENES 🔪
Simple: This node intelligently splits the full list of scenes into individual data items, one for each scene.

Tech: A Code node processes the scenesList array. It assigns a sceneNumber (1 to 6) and cleans up the sceneMessage for each individual scene, preparing them to be processed independently.

🔹 8-13. SCENE 1, SCENE 2, ..., SCENE 6 🎯
Simple: These nodes isolate each specific scene (Scene 1, Scene 2, etc.) from the combined list, allowing each to be processed individually.

Tech: Each of these six Code nodes filters the incoming data to output only the item corresponding to its specific sceneNumber (e.g., scene.sceneNumber === 1 for "SCENE 1"), ensuring each subsequent image generation and sending process handles one scene at a time.

🔹 14-19. IMAGE URL GENERATION, IMAGE URL GENERATION1, ..., IMAGE URL GENERATION4 🖼️
Simple: For each scene, this node sends the "Image Prompt" from the script to an AI image generator to get a picture.

Tech: These HTTP Request nodes make POST requests to the Together API (https://api.together.xyz/v1/images/generations). They are configured to send application/json with Bearer token authentication and Accept header set to image/png. The jsonBody dynamically extracts the Image Prompt from the sceneMessage using a regular expression ($json.sceneMessage.match(/- Image Prompt:(.*?)(- Motion Prompt:|- Visual Effects:|$)/s)?.[1].trim() || '') and specifies the black-forest-labs/FLUX.1-schnell model with dimensions of 432x768 pixels.

🔹 20-25. IMAGE DOWNLOAD, IMAGE DOWNLOAD, ..., HTTP Request11 ⬇️
Simple: Once the image URL is generated, these nodes download the actual image file.

Tech: These HTTP Request nodes perform GET requests to the image URL obtained from the previous image generation step ($json.body.data[0].url). The responseFormat is set to file to download the image binary data directly.

🔹 26-31. Send a photo message, Send a photo message1, ..., Send a photo message5 📲
Simple: Finally, these nodes send the downloaded images, along with their respective scene captions, to your Telegram chat.

Tech: These Telegram nodes use the sendPhoto operation. They require a chatId and are configured to send binaryData (the downloaded image). The caption for each photo is dynamically set to "SCENE 1", "SCENE 2", etc., indicating which scene the image represents. This node uses a pre-configured Telegram API credential.

🔁 Replace the following before running:
🔐 YOUR_OPENROUTER_BEARER_TOKEN
Replace this with your personal OpenRouter Bearer token in the SCRIPT node's Authorization header.

🔐 YOUR_TOGETHER_API_KEY
Replace this with your personal Together API key in the Authorization header of all IMAGE URL GENERATION nodes.

💬 YOUR_TELEGRAM_CHAT_ID
Insert your personal Telegram chat ID in all Send a photo message nodes.

🤖 YOUR_TELEGRAM_BOT_TOKEN
Configure your Telegram bot token under the Credentials section in n8n for the Telegram API credential named "SCRIPT FOR VEDIO".

🧪 Testing Instructions
To get this workflow up and running:

✅ Open n8n at http://localhost:5678 (or wherever your n8n instance is hosted).
🔧 In the n8n interface, click the "Import Workflow" button (usually located in the top right or within the workflows list).
🔗 Paste the provided JSON workflow into the import dialog and click "Import".
⚙️ Before executing, replace all placeholder values as instructed in the "Replace These Values" section above.
🧪 To test individual steps, you can click on any node and then click "Execute Node". This lets you see the input and output of each step.
▶️ To run the entire workflow, click the "Execute Workflow" button in the top right corner.
🪵 Check the execution log (accessible via the "Executions" tab) to troubleshoot any issues or errors that may occur during the run.
📱 Verify that the images are being sent to your specified Telegram chat.

💡 Tips for Beginners
💡 Use the “Credentials” tab in n8n to set up your Telegram API and other API keys securely. This keeps your sensitive information separate from the workflow itself.
💡 After executing each node (especially the "SCRIPT" and "IMAGE URL GENERATION" nodes), check the node outputs to verify that the data is flowing as expected and that the API calls are returning valid responses. This is crucial for debugging.
💡 You can adjust the Schedule Trigger node to change when the workflow runs, e.g., daily at a different time, or even hourly, to fit your preferences.
💡 If an image isn't appearing in Telegram, first check the HTTP Request nodes for image generation and download for any errors in the response or URL.
💡 Always ensure your API keys have the necessary permissions for the actions the workflow is trying to perform (e.g., generating images, sending Telegram messages).












Canvas


