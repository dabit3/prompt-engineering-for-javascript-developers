# Prompt engineering cheatsheet (for JavaScript developers)

Notes summarized from [this tutorial](https://learn.deeplearning.ai/chatgpt-prompt-eng/).

## Principle 1: Write clear and specific instructions

### Tactic 1: Use delimiters to clearly indicate distinct parts of the input

Delimiters can be anything like this:

```
'''text'''

"""text"""

< text >

<tag>text</tag>
```

For instance:

```javascript
const text = `
You should express what you want a model to do by \ 
providing instructions that are as clear and \ 
specific as you can possibly make them. \ 
This will guide the model towards the desired output, \ 
and reduce the chances of receiving irrelevant \ 
or incorrect responses. Don't confuse writing a \ 
clear prompt with writing a short prompt. \ 
In many cases, longer prompts provide more clarity \ 
and context for the model, which can lead to \ 
more detailed and relevant outputs.
`

const prompt = `
Summarize the text delimited by triple quotes into a single sentence.
"""${text}"""
`
```

### Tactic 2: Ask for a structured output

Like JSON or HTML

```javascript
const prompt = `
Generate a list of three made-up book titles along \ 
with their authors and genres. 
Provide them in JSON format with the following keys: 
book_id, title, author, genre.
`
```

### Tactic 3: Ask the model to check whether conditions are satisfied

```javascript
const text = `
Making a cup of tea is easy! First, you need to get some \ 
water boiling. While that's happening, \ 
grab a cup and put a tea bag in it. Once the water is \ 
hot enough, just pour it over the tea bag. \ 
Let it sit for a bit so the tea can steep. After a \ 
few minutes, take out the tea bag. If you \ 
like, you can add some sugar or milk to taste. \ 
And that's it! You've got yourself a delicious \ 
cup of tea to enjoy.
`

const prompt = `
You will be provided with text delimited by triple quotes. 
If it contains a sequence of instructions, \ 
re-write those instructions in the following format:

Step 1 - ...
Step 2 - ...
...
Step N - ...

If the text does not contain a sequence of instructions, \ 
then simply write \"No steps provided.\"

"""${text}"""
`
```

### Tactic 4: "Few-shot" prompting

"Few-shot" prompting refers to providing a limited number of examples to an AI model, then having it generate new responses in a similar domain. It's a technique commonly used for training and utilizing large language models (LLMs).

The steps for few-shot prompting are:

1. Select a domain or topic you want the model to generate responses for. This could be a genre of text, way of speaking, etc.

2. Provide a small set of examples (the "prompts") from that domain for the model to condition on. Usually just 2-5 examples is sufficient for "few-shot" learning.

3. The model will analyze the patterns, styles, and structures in the prompts. It will learn the attributes that define responses in that domain.

4. Have the model generate a new response in that same domain. By conditioning on the prompts, it can produce a response that matches the desired style, structure, etc.

5. Review the response and provide feedback to further improve the model. This can either be explicit feedback to the model, or just note areas for generating the next set of prompts.

#### Example:

```javascript
const prompt = `
Your task is to answer in a consistent style.

<child>: Teach me about patience.

<grandparent>: The river that carves the deepest \ 
valley flows from a modest spring; the \ 
grandest symphony originates from a single note; \ 
the most intricate tapestry begins with a solitary thread.

<child>: Teach me about resilience.
`
```

## Principle 2: Give the model time to “think”

### Tactic 1: Specify the steps required to complete a task

```javascript
const text = `
In a charming village, siblings Jack and Jill set out on \ 
a quest to fetch water from a hilltop \ 
well. As they climbed, singing joyfully, misfortune \ 
struck—Jack tripped on a stone and tumbled \ 
down the hill, with Jill following suit. \ 
Though slightly battered, the pair returned home to \ 
comforting embraces. Despite the mishap, \ 
their adventurous spirits remained undimmed, and they \ 
continued exploring with delight.
`

// example 1
const prompt_1 = `
Perform the following actions: 
1 - Summarize the following text delimited by triple quotes with 1 sentence.
2 - Translate the summary into French.
3 - List each name in the French summary.
4 - Output a json object that contains the following keys: french_summary, num_names.

Separate your answers with line breaks.

Text:
"""${text}"""
`
```

### Tactic 2: Instruct the model to work out its own solution before rushing to a conclusion

```javascript
const prompt = `
Your task is to determine if the student's solution \
is correct or not.
To solve the problem do the following:
- First, work out your own solution to the problem. 
- Then compare your solution to the student's solution \ 
and evaluate if the student's solution is correct or not. 
Don't decide if the student's solution is correct until 
you have done the problem yourself.

Use the following format:

Question:

"""
question here
""

Student's solution:

"""
student's solution here
"""

Actual solution:

"""
steps to work out the solution and your solution here
"""

Is the student's solution the same as actual solution just calculated:

""
yes or no
"""

Student grade:

"""
correct or incorrect
"""

Question:

"""
I'm building a solar power installation and I need help \
working out the financials. 
- Land costs $100 / square foot
- I can buy solar panels for $250 / square foot
- I negotiated a contract for maintenance that will cost \
me a flat $100k per year, and an additional $10 / square \
foot
What is the total cost for the first year of operations \
as a function of the number of square feet.
"""

Student's solution:

"""
Let x be the size of the installation in square feet.
Costs:
1. Land cost: 100x
2. Solar panel cost: 250x
3. Maintenance cost: 100,000 + 100x
Total cost: 100x + 250x + 100,000 + 100x = 450x + 100,000
"""

Actual solution:
`
```

## Iterative Prompt Develelopment

Rarely will your first prompt work as effectively as you'd like the first time (duh!).

Instead, recognize that it usually takes quite a few iterations before you can get it a place that works as well as you'd like. 

## Summarizing

### Summarize with a word/sentence/character limit

```javascript
const prod_review = `
Got this panda plush toy for my daughter's birthday, \
who loves it and takes it everywhere. It's soft and \ 
super cute, and its face has a friendly look. It's \ 
a bit small for what I paid though. I think there \ 
might be other options that are bigger for the \ 
same price. It arrived a day earlier than expected, \ 
so I got to play with it myself before I gave it \ 
to her.
`

const prompt = `
Your task is to generate a short summary of a product review from an ecommerce site. 

Summarize the review below, delimited by triple quotes, in at most 30 words. 

Review: """${prod_review}"""
`
```

## Summarize with a focus

```javascript
const prompt = `
Your task is to generate a short summary of a product \
review from an ecommerce site to give feedback to the \
Shipping deparmtment. 

Summarize the review below, delimited by triple \
quotes, in at most 30 words, and focusing on any aspects \
that mention shipping and delivery of the product. 

Review: """${prod_review}"""
`
```

## Try "extract" instead of "summarize"

```javascript
const prompt = `
Your task is to extract relevant information from \ 
a product review from an ecommerce site to give \
feedback to the Shipping department. 

From the review below, delimited by triple quotes \
extract the information relevant to shipping and \ 
delivery. Limit to 30 words. 

Review: """${prod_review}"""
`
```

## Inferring

Infer sentiment and topics from given text.

### Sentiment analysis

```javascript
const prod_review = `
Needed a nice lamp for my bedroom, and this one had \
additional storage and not too high of a price point. \
Got it fast.  The string to our lamp broke during the \
transit and the company happily sent over a new one. \
Came within a few days as well. It was easy to put \
together.  I had a missing part, so I contacted their \
support and they very quickly got me the missing piece! \
Lumina seems to me to be a great company that cares \
about their customers and products!!
`

const prompt = `
What is the sentiment of the following product review, 
which is delimited with triple quotes?

Review text: """${prod_review}"""
`

const prompt = `
What is the sentiment of the following product review, 
which is delimited with triple quotes?

Give your answer as a single word, either "positive" \
or "negative".

Review text: """${prod_review}"""
`
```

### Types of emotions

```javascript
const prompt = `
Identify a list of emotions that the writer of the \
following review is expressing. Include no more than \
five items in the list. Format your answer as a list of \
lower-case words separated by commas.

Review text: """${prod_review}"""
`

const prompt = `
Is the writer of the following review expressing anger?\
The review is delimited with triple quotes. \
Give your answer as either yes or no.

Review text: """${prod_review}"""
`
```

### Extracting data

```javascript
const prompt = `
Identify the following items from the review text: 
- Item purchased by reviewer
- Company that made the item

The review is delimited with triple quotes. \
Format your response as a JSON object with \
"Item" and "Brand" as the keys. 
If the information isn't present, use "unknown" \
as the value.
Make your response as short as possible.
  
Review text: """${prod_review}"""
`
```

### Multiple tasks at once

```javascript
const prompt = `
Identify the following items from the review text: 
- Sentiment (positive or negative)
- Is the reviewer expressing anger? (true or false)
- Item purchased by reviewer
- Company that made the item

The review is delimited with triple quotes. \
Format your response as a JSON object with \
"Sentiment", "Anger", "Item" and "Brand" as the keys.
If the information isn't present, use "unknown" \
as the value.
Make your response as short as possible.
Format the Anger value as a boolean.

Review text: """${prod_review}"""
`
```

### Inferring topics

```javascript
const story = `
In a recent survey conducted by the government, 
public sector employees were asked to rate their level 
of satisfaction with the department they work at. 
The results revealed that NASA was the most popular 
department with a satisfaction rating of 95%.

One NASA employee, John Smith, commented on the findings, 
stating, "I'm not surprised that NASA came out on top. 
It's a great place to work with amazing people and 
incredible opportunities. I'm proud to be a part of 
such an innovative organization."

The results were also welcomed by NASA's management team, 
with Director Tom Johnson stating, "We are thrilled to 
hear that our employees are satisfied with their work at NASA. 
We have a talented and dedicated team who work tirelessly 
to achieve our goals, and it's fantastic to see that their 
hard work is paying off."

The survey also revealed that the 
Social Security Administration had the lowest satisfaction 
rating, with only 45% of employees indicating they were 
satisfied with their job. The government has pledged to 
address the concerns raised by employees in the survey and 
work towards improving job satisfaction across all departments.
`

const prompt = `
Determine five topics that are being discussed in the \
following text, which is delimited by triple quotes.

Make each item one or two words long. 

Format your response as a list of items separated by commas.

Text sample: """${story}"""
`
```

## Transforming

How to use LLMs for text transformation tasks such as language translation, spelling and grammar checking, tone adjustment, and format conversion.

Models have the ability to do language translation.

```javascript
const prompt = `
Translate the following English text to Spanish:

"""Hi, I would like to order a blender"""
`

const prompt = `
Tell me which language this is: 
"""Combien coûte le lampadaire?"""
`
```

### Tone Transformation

Writing can vary based on the intended audience. ChatGPT can produce different tones.

```javascript
const prompt = `
Translate the following from slang to a business letter: 
'Dude, This is Joe, check out this spec on this standing lamp.'
`
```

### Format Conversion

ChatGPT can translate between formats. The prompt should describe the input and output formats.

```javascript
const json = {
  "resturant employees" :[ 
    {"name":"Shyam", "email":"shyamjaiswal@gmail.com"},
    {"name":"Bob", "email":"bob32@gmail.com"},
    {"name":"Jai", "email":"jai87@gmail.com"}
  ]
}

const prompt = `
Translate the following python dictionary from JSON to an HTML \
table with column headers and title: ${json}
`
```

### Spellcheck / grammar check and proofreading

```javascript
const prompt = `
Proofread and correct the following text
and rewrite the corrected version. If you don't find
and errors, just say "No errors found". Don't use 
any punctuation around the text:

"""${text}"""
`

const prompt = `
proofread and correct this review. Make it more compelling. 
Ensure it follows APA style guide and targets an advanced reader. 
Output in markdown format.

Text: """${text}"""
`
```

## Expanding

Taking a small group of text to expand it into a much larger group of text.

### Customizing the automated reply to a customer email

```javascript
const review = `
So, they still had the 17 piece system on seasonal \
sale for around $49 in the month of November, about \
half off, but for some reason (call it price gouging) \
around the second week of December the prices all went \
up to about anywhere from between $70-$89 for the same \
system. And the 11 piece system went up around $10 or \
so in price also from the earlier sale price of $29. \
So it looks okay, but if you look at the base, the part \
where the blade locks into place doesn’t look as good \
as in previous editions from a few years ago, but I \
plan to be very gentle with it (example, I crush \
very hard items like beans, ice, rice, etc. in the \ 
blender first then pulverize them in the serving size \
I want in the blender then switch to the whipping \
blade for a finer flour, and use the cross cutting blade \
first when making smoothies, then use the flat blade \
if I need them finer/less pulpy). Special tip when making \
smoothies, finely cut and freeze the fruits and \
vegetables (if using spinach-lightly stew soften the \ 
spinach then freeze until ready for use-and if making \
sorbet, use a small to medium sized food processor) \ 
that you plan to use that way you can avoid adding so \
much ice if at all-when making your smoothie. \
After about a year, the motor was making a funny noise. \
I called customer service but the warranty expired \
already, so I had to buy another one. FYI: The overall \
quality has gone done in these types of products, so \
they are kind of counting on brand recognition and \
consumer loyalty to maintain sales. Got it in about \
two days.
`

const prompt = `
You are a customer service AI assistant.
Your task is to send an email reply to a valued customer.
Given the customer email delimited by """, \
Generate a reply to thank the customer for their review.
If the sentiment is positive or neutral, thank them for \
their review.
If the sentiment is negative, apologize and suggest that \
they can reach out to customer service. 
Make sure to use specific details from the review.
Write in a concise and professional tone.
Sign the email as 'AI customer agent'.
Customer review: """${review}"""
`
```

## Chatbot

Explore how you can utilize the chat format to have extended conversations with chatbots personalized or specialized for specific tasks or behaviors.

### OrderBot

We can automate the collection of user prompts and assistant responses to build a OrderBot. The OrderBot will take orders at a pizza restaurant.

```javascript
const prompt = `
  You are OrderBot, an automated service to collect orders for a pizza restaurant. \
  You first greet the customer if they ask an empty or introductory question, then collect the order, \
  and then ask if it's a pickup or delivery. \
  You wait to collect the entire order, then summarize it and check for a final \
  time if the customer wants to add anything else. \
  If it's a delivery, you ask for an address. \
  Finally you collect the payment.\
  Make sure to clarify all options, extras and sizes to uniquely \
  identify the item from the menu.\

  You will be provided with "previous conversation" data if it exists so you have context about what was alrady discussed.

  You respond in a short, very conversational friendly style. \
  The menu includes \
  pepperoni pizza  12.95, 10.00, 7.00 \
  cheese pizza   10.95, 9.25, 6.50 \
  eggplant pizza   11.95, 9.75, 6.75 \
  fries 4.50, 3.50 \
  greek salad 7.25 \
  Toppings: \
  extra cheese 2.00, \
  mushrooms 1.50 \
  sausage 3.00 \
  canadian bacon 3.50 \
  AI sauce 1.50 \
  peppers 1.00 \
  Drinks: \
  coke 3.00, 2.00, 1.00 \
  sprite 3.00, 2.00, 1.00 \
  bottled water 5.00

  """
  previous conversation: ${oldContext}

  question for this order: ${newContext}
  """
`
```
