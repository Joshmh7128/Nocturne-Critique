#nocturne #outpost4 
Backlinked: [[Nocturne Technical Critique]], [[Nocturne Technical & Design Critique Homepage]]
# Introduction
As a general rule in Unity & C# it is best practice to adhere to the [Common C# Code Conventions.](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)While it is not necessary from a technical standpoint, as a new developer entering the project I'm finding it quite difficult to adopt and understand aspects of the code. This ranges from the agnostic use of camel casing, lack of comments, summaries, and subsequent reasoning behind documentation. Other readability critiques include unnecessary empty lines, cramped statements, commented lines of old code, inconsistent spacing in mathematics, undescriptive and unused variables.

**This entire page should be considered** #actionable-critique
## Lack of Comments
Throughout the project there are a distinct lack of comments associated with functions, variables, and parameters. In many cases entire sections are unexplained, resulting in a complete lack of readability. Many examples of this can be seen through ``Nocturne.cs`` and ``Score.cs`` as most of the game runs off of these files. While it is possible for one to find and track all the references of each function and variable, this takes up unnecessary time and energy which could be solved with a short hand comment.
## Lack of Summaries
As mentioned above more advanced functions appear to be lacking summaries explaning their functionality. This is makes functions especially difficult to understand when they have complex mathematical statements in them. Shorthand summaries which explain rationale for how aspects of functions are setup will help exponentially with the processing and understanding of each aspect of code.
## Lack of Parameters
This is specifically for advanced and 
## Uninformative Variable Names
## Lack of Reasoning or Rationale
## Unnecessary empty lines
Example beginning on line 579 of ``Nocturne.cs``

```
//This function determines if a note is being pressed in.

  

public float howPressed(float thisNote, float widen = 1, bool setkey = true, bool two = false)...  
```

Although the comment clearly refers to the float function below, the readability is significantly hindered by the spacing. It creates a far less readable environment.
## Cramped Statements
Lines 567 to 588 in ``Score.cs``
```
if (previousNote != null && previousNote.end <= note.start && startNow > 0 && noteConnections && startNow < 16f)
{
    //color2 = nocturne.noteColor((int)previousNote.note);
    //Vector3 start2 = GetNotePosition(previousNote.note, Now(note.start));
    //Vector3 end2 = GetNotePosition(note.note, Now(note.start));

    //Draw.Line(start2, end2, thickness: .007f, color2 * .3f, color * .2f);
    //Draw.Line(start2, end2, thickness: .007f, noteLine, noteLine);
    PolylinePath path = new PolylinePath();
    length = Mathf.Abs(previousNote.note - note.note) + 1;
    float colorFade = -4f * Mathf.Pow((1 - startNow / 16 - .5f), 2) + 1f;
    for (float i = 0; i <= 1.02f; i += 1/length)
    {
        float pitch = Mathf.Lerp(previousNote.note, note.note, i);
        Vector3 position = GetNotePosition(pitch, Now(note.start));
        path.AddPoint(position, i, color * i * colorFade);
    }
    Draw.Polyline(path, false,thickness:.01f, PolylineJoins.Simple);
    path.Dispose();
    if (startNow < 0 && endNow > 0) Draw.Sphere(GetNotePosition(nextNote.note, 0f), .01f, Color.grey);

}
```
## Inconsistent Ternary Operator Presentation
## Agnostic Camel & Snake Casing
## Old Lines in Comments
## Lack of Regions
## Inconsistent Mathematical Statement Presentation
Take this example from line 561 of ``Nocturne.cs``
```
float freq = Mathf.Pow(2, (theEndPitch - 69) / 12f);
```
and compare it to 
## Undescriptive & Unused Variables
