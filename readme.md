# Toriality's DevLogs

**Here I'll share all my projects' progress!**

<details>
  <summary>DevLogs Archives</summary>

- 2023
  - [January](archive/2023-01.md)
  - [February](archive/2023-02.md)
  - [March](/archive/2023-03.md)
  - [April](/archive/2023-04.md)

</details>

## DevLog #26 - 06.05.2023

It's hard to believe we're already in May, time seems to be flying this year.

After taking a short break from writing the devlogs this last week, I'm excited to share some interesting news!

### DYOM Server

In my last devlog, I mentioned that I was planning on refactoring a significant portion of the server, focusing on the database and its validations.

I'm happy to report that I've now replaced almost all of the form validations in the server with pre-established functions provided by the Mongoose framework. This has reduced the code length and repetitions, reduced its complexity, and improved the security of the server.

### Chronotune (New Project)

Over the last three days, I've been working on a new project called Chronotune. The premise is similar to Chronophoto, but instead of guessing the year a photo was taken, users must guess the year the given random song was released.

To accomplish this, Chronotune calls the Spotify API and searches for random songs that meet specific criteria. Currently, the criteria are as follows: the song must have a popularity score of 10 or higher, it must have been released between 1900 and the current year, and theres a 50% chance of the query to include one random English dictionary word.

Here's a sneak peek at the site's current state:

![ct1](https://i.imgur.com/uRLpJJO.png)

On the server side, I've implemented logging functionality to capture relevant information:

![ct2](https://i.imgur.com/FQPyFnd.png)

The site is now ready for testing, and I invite you to check it out at https://toriality.github.io/chronotune/.

As always, your feedback is welcome and appreciated :wink:
