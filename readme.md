# Toriality's DevLogs

**Here I'll share all my projects' progress!**

<details>
  <summary>DevLogs Archives</summary>

- 2023
  - [January](archive/2023-01.md)
  - [February](archive/2023-02.md)
  - [March](/archive/2023-03.md)

</details>

## DevLog #21 - 17.04.2023

### DYOM Server

Just finished another restructuring of the server and database. Now, the Projects collection has the Missions schema inside.

Imagine a streaming platform where each TV show is a project, and each episode of the show is a mission within that project. Each episode (mission) would have fields like video files, summary, and views statistics. In this example, a TV show project could be "Breaking Bad", and the missions within that project would be individual episodes, such as "Ozymandias" "I.F.T." "Fly" and so on.

Although it is an extra level of complexity, this approach brings some very interesting benefits that have never been explored in the DYOM community before. Let me introduce you these benefits!

#### Mission Statistics

Imagine a DYOM mission that takes place in a large war zone with many enemies to eliminate, or a chase mission where you had to be fast to reach your objective, or perhaps a racing mission. You may have already done some missions that fit into one or more of the styles mentioned above.

Now, imagine if there was a way to know, in exact numbers, how many people played your mission and failed or won, what was the fastest or slowest play, the average time it takes players to complete your mission, and many other statistics. All of this is possible with this new system.

Of course, the server alone would not be able to report what the players do in-game because it does not have access to their computer. But together with the help of another developer, **Miran**, we are making a new project that covers a new DYOM server and also a new DLL file that is activated while you play DYOM on your computer and communicates in real-time with the server, making this statistics system (and many others we are planning) possible.

![miran_dll](https://user-images.githubusercontent.com/38092988/232642751-31ddfbe2-5bf6-4f90-b157-7855361598d6.png)

#### DYOM Standardization 

Finally, another benefit that this new structure brings is standardization of DYOM projects. Currently, you can upload a .rar file that contains literally anything inside, as long as it does not exceed 8 MB; everything is allowed in it. In the new system, this will be very different.

In our new system, when creating a project, the form will be divided into smaller sections, each corresponding to a specific aspect of the project, such as the SD section, the mission section, and so on. This means that users will only be able to upload files that are relevant to each section. For example, in the SD section, only files that would normally be found in the SD folder will be allowed, and in the mission section, only .dat files will be valid.

After a user submits their project, the server will collect all the relevant files and organize them into logical directories. When a user downloads the mission, the server will package all the necessary files into a .rar file and deliver it to the end-user. The package will even include a default readme.txt file that provides instructions on how to install the mission, simplifying the process even more!

Here's a screenshot taken today from the database demonstrating an example project:

![MongoDBCompass_mhzwU9pxw1](https://user-images.githubusercontent.com/38092988/232642780-586de765-39d0-4e0c-842f-a07125d00dbd.png)

However, there are some issues that will need to be resolved in the coming days:

- I forgot to make a field to set SD code :neutral_face:	
- SD and modloader folders have the CRC values of uploaded files, but they don't store the original names of these files, which makes them useless since the game wouldn't be able to read them properly.

## DevLog #20 - 15.04.2023

### DYOM Bingo

There was a pending issue in the repo regarding the calculation of the timer on DYOM Bingo. The issue was related to the timer and PB text being updated on every tick call, which occurs every 200ms. Thats not an ideal approach in terms of performance.

I implemented a new logic where these DOM elements only update when necessary. As a result, the timer is now going to update every second, and the PB updates only when the user gets a new high score for PB.

[Take a look at the Pull Request to have a more in-depth view of the changes made!](https://github.com/Toriality/DYOM-Bingo/pull/30)

Heres a demonstration of how the DOM was being updated and how it is working currently:

<div align="center"><h4>Before</h4></div>

![opera_E5myK7Ssdf](https://user-images.githubusercontent.com/38092988/232260167-be7476d5-2381-49bc-8917-44cbb6458864.gif)

<div align="center"><h4>After</h4></div>

![opera_uCiGau8A1y](https://user-images.githubusercontent.com/38092988/232260172-22302c95-abc8-49ca-a7bc-306ee4854006.gif)


### DYOM Website

As I mentioned yesterday, I'll be working on restructuring the way files are stored on the server.

So far I have managed to implement methods of saving the files with their respective CRC name and storing the CRC value in the database for easy access.

Additionally, I have implemented a file validation function that checks if the .dat file entered is a valid DYOM file. This validation process involves reading the first 4 bytes of the file and converting them into an unsigned 32-bit integer, which should return a number 6 for all DYOM 8.1 missions. If the file doesn't return 6, it will be invalidated.

```js
async function validateFiles(req, res, next) {
  const DYOM_NUM = 6;
  const file = req.files.file[0];
  const fileData = fs.openSync(file.path, "r");
  const buf = Buffer.alloc(4);
  fs.readSync(fileData, buf, 0, 4, 0);
  const fileNum = buf.readUInt32LE(0);
  fs.closeSync(fileData);

  if (fileNum == DYOM_NUM) {
    next();
  } else {
    res.status(400).json({ msg: "File must be a valid DYOM mission.")
  }
}
```

Tomorrow, I plan to further improve these functions and apply them to routes for downloading files, getting an random mission from database, among others routes I should update with this new method.

## DevLog #19 - 14.04.2023

### DYOM Bingo

Today was a day of organization in the DYOM Bingo repository. I made a total adjustment to the repository's [README.md](https://github.com/Toriality/DYOM-Bingo), made it prettier and more readable, and the main thing, added the all-contributors to the project.

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
