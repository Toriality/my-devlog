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

## DevLog #27 - 09.05.2023

Many new features are currently being developed for Chronotune. In this post, I will list all the new features that are ready or almost ready to be released in the next version of the site, which should be available in a few days.

### Title Menu

A title menu for the site, with a simple design but with some unique features that have the Chronotune essence.

On this page we see the title and description of the site in a big font, with some rather oversized text shading. And we see buttons with a vibrant yellow, black border and shadow. It's a mix of the vibrant Spotify visuals with a more exaggerated neubrutalist feel.

One of the features that stands out on this page is its background. Within a second after the page is done loading, the background displays 50 different images with a slight blur effect and a smoothly slide transition animation.

![opera_paHCP8nwHy](https://github.com/Toriality/my-devlog/assets/38092988/760ea80c-83d2-42d9-9e4f-dcabca90a1bc)


### Database: Invalid queries

This is not visible to the user, but while he is loading a random song, several calls to the Spotify API are made, with a random word, year and offset. Often this random combination results in no songs being found, or some invalid result. And in order not to exceed Spotify's API call per second limits, for each invalid query, it waits 3 seconds for a new query. Meanwhile all of this is happening, our user is still waiting on the loading screen.

In an attempt to reduce the user's waiting time, one of the alternatives found (but not heavily tested yet) is to include invalid queries in the site's database. Let's suppose the following query;

```json
{
"year": 1903,
"offset": 883,
"word": "dust"
}
```

This query is called in the Spotify API, which returns no results. So this query is added to the database of invalid queries.

The next time a user gets this same query, the site won't bother calling the Spotify API, because it already knows that the query is invalid, and will generate a completely new query automatically, with no waiting time.

### Refactoring

Today, I spent the day cleaning up the code, particularly on the server-side. Although it is not yet complete, we are on the right track. The code has fewer repetitions and is better documented.

For example, as I said above, when we search for a random song the server waits 3 seconds to search again. However, we cannot leave the server endlessly attempting to perform this task. Every task that requires retries needs a timeout and a limited number of retries. In other words, the function to fetch a random song, generate a new access token, or generate a new image variant all require a retry functionality.

Previously, the same logic was being written repeatedly in each of these functions. After today's refactoring, a high-order function is called on all of these routes, which can be customized as needed, reducing unnecessary repetition and consolidating all the retentive logic in a single space.

That's it for today. I'm excited to release the new version of Chornotune soon, with many cool new features and fixes, which I'll keep reporting them here as they progress their development.

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
