<img src="https://emojipedia-us.s3.amazonaws.com/thumbs/240/mozilla/36/memo_1f4dd.png" align="right" width="80">

# JQuizz
> Lightweight app to read/write quiz with JSON

Need to practice for an exam or just want to test your friends or whatever..? Do it thanks to this tool which allows you to consult, upload and create your own quiz.

This tool was created with [Vue](https://vuejs.org/) and [Boostrap](https://getbootstrap.com/).

## Features
Create your own quiz with different types of questions including images or videos. 
You can also upload an existing quiz in json format that you have been given or previously saved. 

## How to use it 
- [Quizz format](#format)
- [Create](#create)
- [Upload](#upload)

### <a id="format"/> Quizz format
A quiz is composed of one or more parts containing questions. Here is an example:
```json
{
  "title": "Quizz's title",
  "instructions": "Quizz's instructions",
  "quiz": [{
      "name": "Part 1",
      "questions": [{
          "text": "Input question of Part 1",
          "type": "input",
          "answers": ["answer 1", "answer 2"],
          "image": "https://images.unsplash.com/photo-1532455935509-eb76842cee50?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=91b5904019e2c1448a0609eba936ef81&auto=format&fit=crop&w=2251&q=80"
        },
        {
          "text": "Radio question of Part 2",
          "type": "radio",
          "propal": [
            "answer 1",
            "answer 2",
            "answer 3"
          ],
          "answers": [
            "answer 2"
          ]
        }
      ]
    },
    {
      "name": "Part 2",
      "questions": [{
          "text": "Input question of Part 2",
          "type": "input",
          "answers": ["answer 1"]
        }
      ]
    }
  ]
}

```

### <a id="create"/> Create
Start by naming your quiz and adding instructions if necessary and click next.

Give a name to the first part of your quiz, select the type, write your question, the answers and click on **Add Question**. After you have added all the questions for your game, finalize it by clicking on **Add Part**.

You can now preview your quiz by clicking on **JSON Preview**.

Continue adding questions/parts or finalize your quiz by clicking on **Finish**, the file containing your quiz will then start downloading.

### <a id="upload"/> Upload
Upload a valid JSON file by following the example above or by creating it from the create function.