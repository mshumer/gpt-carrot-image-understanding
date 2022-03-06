# gpt-carrot-image-understanding
Using GPT-3 and Carrot (GPT-3 for computer vision) to create detailed descriptions of images.


Large-scale AI models have taken the world by storm in recent years. GPT-3, the first widely accessible large-scale AI model, was a step change in the field of AI. It's flexible enough to do nearly anything -- you just need to figure out how to ask it to do what you want. But GPT-3 is also limited to language -- it wasn't trained on images or other forms of data.


The success of GPT-3 has led to a new wave of massive AI models that work across different domains. We've seen DALL-E and CLIP, which can generate and classify images. We've also seen models that can work with both text and images, though these models are still in the early stages of development.


In February 2022, Banana.dev (full disclosure -- I'm a small investor in Banana) set up an open source ML research community, Plantain Labs. Plantain Labs' first model, Carrot, is a general-purpose computer vision model that can answer questions about an image. Carrot is surprisingly powerful, and is quite accurate in its answers.


When I first got the chance to try out Carrot, I wanted to push it to the limit to see what I could do with it. My first target was image understanding -- I wanted to see how we could use Carrot to generate a highly detailed description of an image.


So, I combined Carrot with GPT-3, and the results were surprisingly good. It's nowhere near perfect yet -- often, the model will get things wrong, or will miss key details, but I only had a couple of hours to put this all together, so there's a lot of room for improvement (see below for things to work on).


Here are some examples:

![FM5J_ulVUAAKovz](https://user-images.githubusercontent.com/41550495/156906534-97242518-a645-4e57-8c2d-a9e34876a3e2.png)

**This is a man working on a computer. The man has hair and is sitting down. His shirt is blue and his computer is silver. The computer is on and the man is typing on the keyboard. The computer screen is displaying code.
**

![FM5NT3xVUAEDrVw](https://user-images.githubusercontent.com/41550495/156906547-920e2910-ca30-4834-b2c3-d4906893e118.jpeg)

**This is a photograph of a tennis player. The tennis player is in the process of hitting a ball. He is wearing blue shorts and a blue shirt. The ball is in the air, and the player is using his right hand to swing the racket.
**

If you want to try it out yourself, you can use either the Python file or notebook in this repo, or you can use this link to access the Colab version (note -- the Colab won't be updated as this repo is updated): https://colab.research.google.com/drive/1-k7CQX7kp6SEHLtF6XYSsJ7RvxCtgtLx?usp=sharing


If you're curious about how this works, here's a brief overview. It's a bit complicated, and has many layers, but once you understand it, you'll see why I made the decisions I made:
- I used GPT-3 to simulate a "game" between two people -- one (A) is holding an image, the other (B) is trying to deeply understand what the image is without seeing it. (B) asks questions to (A). 
- Those questions are funneled to Carrot, which provides answers to them.
- The answers are transformed to fit the context of the conversation, and then we continue to ask more questions and get answers to finish the game.
- We repeat this game a bunch of times so it gets really deep and focuses on different aspects of the image.
- I then extract only the transformed answers to the questions, and put them in a list and deduplicate.
- I then do a fact-checking layer, where I use GPT-3 to generate an inverse of the transformed answers (i.e. if the answer is "The bird is on the car.", it generates "Is there a bird on the car?".
- I send each of those to Carrot, and if they check out, we keep them, and if not, we remove them.
- The last layer then takes all the (supposedly) correct answers and uses GPT-3 to transform them into a final, human-like paragraph response.


This method works surprisingly well, but there's a lot that could be improved:
- I was thinking of adding a final layer where we repeat this process a few times, and then use a CLIP model to find the highest-ranked paragraph.
- Each of the prompts were written really quickly, just to demonstrate this as a proof-of-concept. Some time spent on each prompt will drastically improve the quality of the final result.
- The fact-checking layer can be improved -- it gets things wrong sometimes. We might want to run the Carrot call a few times instead of just once.
- The final descriptions sometimes contain negative statements (i.e. "He has brown hair. He is not bald."). We might want to consider removing these from the final descriptions (in this case, "He is not bald.") as they don't add anything to the final result.


Anyway, I wasn't able to spend too much time on this since my focus is on building OthersideAI, but I wanted to share what I built so far so that the AI community can continue to improve it. 


There are some really amazing use-cases that are enabled if we can improve the accuracy of the final results... for example, imagine being able to automatically describe any image on the web to someone who is vision impaired.

