# Toriality's DevLogs

**Here I'll share all my projects' progress!**

<details>
  <summary>DevLogs Archives</summary>

- 2023
  - [January](archive/2023-01.md)
  - [February](archive/2023-02.md)
  - [March](/archive/2023-03.md)

</details>

## DevLog #19 - 14.04.2023

### DYOM Bingo

Today was a day of organization in the DYOM Bingo repository. I made a total adjustment to the repository's [README.md](https://github.com/Toriality/DYOM-Bingo/README.md), made it prettier and more readable, and the main thing, added the all-contributors to the project.

[All-contributors](https://allcontributors.org) is a tool that allows contributors to be recognized and credited for their work in a project. It generates a table in the project's README.md file that lists all the contributors and their types of contributions. It is very beneficial because it helps acknowledge and appreciate the efforts of all contributors, regardless of the size or type of their contributions. This can increase the motivation and engagement of contributors and improve the overall project.

![readmemd](https://user-images.githubusercontent.com/38092988/232176929-28dcfd81-b87d-4c83-aa37-3ad55cfde47a.png)

So I'm looking forward to seeing how the contributors interact with this new tool.

### DYOM Webiste

I didn't have much time to work on the backend today, but I can already announce that I'm working on an important change for the backend. It will affect the database and the uploads folder.

Currently, the server-side code validates the uploaded files and stores them in specific folders. However, having multiple folders in the upload folder of a server can lead to several problems, such as increased complexity, reduced scalability, and difficulty in maintenance and backup.

To simplify and optimize the file storage system, I have decided to generate a unique identifier for each file's content by analyzing its content and renaming it to its CRC. [CRC stands for cyclic redundancy check](https://wiki.osdev.org/CRC32), and it is a mathematical algorithm that generates a fixed-size checksum for a file's content.

By using the CRC number as a unique identifier for each file, we can simplify the file storage system and make it more efficient. It allows us to identify duplicates and store them only once in the database, instead of cluttering the server directories with multiple copies of the same file. This not only reduces the amount of storage space required but also makes it easier to manage and scale the system.

Additionally, it enables us to focus on organizing the logic of the files in the database, instead of relying on complex directory structures. This approach also makes it easier to search, retrieve, and manipulate files within the database, as we can query them using a wide range of filters, without worrying about their physical location on the server.

**In the database**, what is previously (well, currently) implemented is the following complex approach:

1. You upload a file
2. It gets stored in a unique folder name
3. The files inside this folder gets analyzed and validated
4. This temporary unique folder is now ready to be renamed to a new folder
5. This new folder is nested into many other directories, let's say, it's something like `./uploads/14/Missions/55` (Where 14 is User ID and 55 is Project ID)
6. The database is informed if the file(s) of type X exist or not.

Let me explain the step 6 better. Let's say a mission has a banner image. After all these steps we tell the Project database that the Project 55 has a banner image by setting the "banner" field to true. Well, you can already see this is not very intuitive and practical specially in a real-life scenario where the original DYOM website has over **67k missions**.

Our goal is to have the following approach on the future:

1. You upload a file
2. It gets stored in the temp folder and renamed to its CRC
3. Each file is validated and analyzed
4. It is moved from the temporary folder to its designated folder based on its type, for example, ./uploads/missions.
5. The database is informed of the file's CRC.

By using this method, instead of relying on a "banner" field to store a true or false value and having to run multiple functions to locate the file, the "banner" field now holds the file's CRC number, making it easier to find and retrieve the file as needed.

**Now in the directory structure**. How will they change exactly?

Currently, our directory structure looks like this:

```
- Uploads
-- 11
--- Missions
---- 39
----- file.dat
----- banner.png
--- temp
---- new_project-i1d8e19
----- file.dat
----- banner.png
-- new_user-2ee9o39
--- avatar.png
```

In the future, the focus is to have a structure like the following:

```
- Uploads
-- Missions
--- 1708735045
--- 5008339925
--- ...
-- Images
--- 0735817041
--- 0770413581
--- ...
```

Overall, the benefits of the new structure are:

- **Simplification:** The new structure simplifies the file storage system by eliminating the need for multiple folders and complex server-side code.
- **Efficiency:** It makes the system more efficient by reducing the amount of code required and increasing the speed of file handling.
- **Scalability:** The new structure is more scalable, as it can handle a large number of files and folders with ease.
- **Maintenance:** The system is easier to maintain and backup, as it has a flat hierarchy and all files are stored in a single directory.

Hopefully, I'll be able to finish most of these implementations on the site :wink:

## DevLog #18 - 12.04.2023

Hello everyone, I had a tiring day today, so I'll keep it short because I'm really looking forward to getting some rest. :smile:

### DYOM Bingo:

This morning, I focused on resolving the Pull Request suggestions from the previous days.

[Click here to check it out.](https://github.com/Toriality/DYOM-Bingo/pull/21)

I'll be awaiting for feedback and testing so that I can move forward with the work.

### DYOM Server:

I'm happy to say that the server is finally finished!

Of course, there are still many features that can be added and plenty of room for improvement, but as I mentioned in my previous DevLogs, my goal was to focus on releasing the essential part of the server.

## DevLog #17 - 10.04.2023

Hello everybody!

Today was quite a productive day for the DYOM Bingo project. We have made some cool new contributions and updates.

Firstly, I started the day by making some changes that I have been wanting to make for quite some time now. I have re-organized our repository and its documentation to make it more user-friendly and... well... organized. Here are the new updates:

- **Introduction to Discussions:** We have inaugurated the Discussions feature provided by GitHub, which now provides us with an page for general discussions. It will be very beneficial for community engagement as it allows us to make suggestions, give general ideas, announce changelogs for new versions, create questions, and make simple polls.

- **Code of Conduct:** We have created a documentation outlining our repository's code of conduct, which provides a comprehensive set of guidelines on how our users should behave, in order to provide an respectful and an inclusion space for everyone in our community. [Click here to check the Code of Conduct.](https://github.com/Toriality/DYOM-Bingo/blob/main/.github/CODE_OF_CONDUCT.md)

- **Contributing Guidelines:** We have created a detailed documentation that provides step-by-step instructions on how new contributors can properly contribute to our project. [Click here to check it out!](https://github.com/Toriality/DYOM-Bingo/blob/main/.github/CONTRIBUTING.md)

- **Templates:** We have created templates for both Issues and Pull Requests, which are optional but recommended since they help our users to format their reports in a more readable and appropriate way.

Additionally, I have also worked on an issue that covered an interesting and useful idea. [This is the requested idea](https://github.com/Toriality/DYOM-Bingo/issues/16). And I was able to open a Pull Request from this Issue on the same day! This was my first time opening a PR from an issue, so I'm really happy to have had this experience.

Overall it has been a pretty interesting day and I'm excited to see what more cool stuff will happen. Thanks for reading :smile:

## DevLog #16 - 09.04.2023

**Greetings everyone,**

It has been more than a month since my last devlog post, and I must admit I was a bit lost about how I'd write another one...

For those who might remember, my last update mentioned about me having contracted COVID-19 and that I was forced to take a break from my projects. Well, I'm happy to report that I recovered from it and I'm feeling much better :)

However, the recovery process took longer than I anticipated, and it took me a good while to get back into the swing of things. Although my motivation to write devlogs was very low, I still managed to make some interesting changes to my projects over the past few weeks.

### DYOM Bingo Updates

With the help of the contributors, I was able to make significant improvements to the site's code, particularly in terms of functionality and file structure. In fact, I have released two new versions of the site within just a month, and more, I am currently working on yet another update.

These new versions contains an improved file architecture and several new things, including a scoreboard, seeds system, card history, and even an easter egg! There are too many new features to list in a single devlog post, so if you're interested, you can check out the complete changelog on this [link](https://toriality.github.io/DYOM-Bingo/changelogs.html), and the new branch that will soon be updated on [here](https://github.com/Toriality/DYOM-Bingo/tree/1.4).

### DYOM Website Updates

To be honest, this project has been giving me some headaches even before I took a break from my projects. It is an ambitious and complex project that is quite challenging for a solo developer to undertake. Sometimes, I honestly wonder if it even makes sense to start from scratch, since that there is already a functioning site which might only requires a few tweaks and fixes to make it good and modernized.

To make things worse, the mod owners have been inactive since 2014-ish (almost a decade!), and there's no guarantee that they would even visualize any of the work I'm doing.

Despite these unanswered questions, I am **not giving up on this**.

My current plan is to focus on creating a fully functional API, with all the fundamental features working correctly. Although the front-end of the site (developed in React) may not be finished, I am determined to complete the back-end (built with NodeJS). So, in the end, we will have a fully working backend for interacting with DYOM's database!

Once both projects are finished, I will not stop contributing to DYOM, ever :). Instead, I will shift my focus to getting a job and improve skills that will help me achieve my career goals.

Thanks for reading, and I'll be back with more updates soon! ;)
