# Director of Data Engineering Homework

## Dataset Explanation
At Hinge, we track **ratings** between users. A rating is any of a number of actions that one user can take on another. In this dataset, the types of ratings are:

| rating_type | meaning                           |
|-------------|-----------------------------------|
| 0           | skip                              |
| 1           | like without a comment            |
| 2           | like with a comment               |
| 3           | remove/block/reject incoming like |
| 4           | report                            |
| 5           | match                             |

We call the person sending the rating the **player** and the person receiving the rating the **subject**. 

If a pair of users has ether never interacted before, or only sent skips between themselves, they are able to perform any rating on each other, except match.

If a User A has sent a like to User B (`rating_type` 1 or 2), User A is unable to send another rating to User B until B responds. B is able to respond only with `rating_type` 3 to reject the incoming like, 4 to report User A for bad behavior, or 5 to match with User A and start chatting.

If User B responds with `rating_type` 5, the two users have matched, and the can start chatting. However, at any point, either of the two users may now send a `rating_type` of 3 to block/remove the other user or 4 to report the other for bad behavior.

If a pair of users has ever shared a `rating_type` of 3 or 4, those two users will forever be unable to access each other in the app, and no more ratings can be sent.

So an example series of ratings might look like this:

| timestamp           | player_id                        | subject_id                       | rating_type |
|---------------------|----------------------------------|----------------------------------|-------------|
| 2018-04-01 00:00:00 | 4a93b6ffc4f0bb16860ca385c8af511b | bb275bbc234a88043097fb9f7e56275a | 0           |
| 2018-04-01 01:00:00 | 4a93b6ffc4f0bb16860ca385c8af511b | bb275bbc234a88043097fb9f7e56275a | 0           |
| 2018-04-01 02:00:00 | 4a93b6ffc4f0bb16860ca385c8af511b | bb275bbc234a88043097fb9f7e56275a | 1           |
| 2018-04-01 03:00:00 | bb275bbc234a88043097fb9f7e56275a | 4a93b6ffc4f0bb16860ca385c8af511b | 5           |
| 2018-04-01 04:00:00 | 4a93b6ffc4f0bb16860ca385c8af511b | bb275bbc234a88043097fb9f7e56275a | 3           |

In this situation, User A skips User B a couple times before finally sending a like. User B reciprocates. User A then looks a little closer at the rest of User B’s photos and removes User B from his/her list of matches, and they never see each other again. 

The files in the S3 bucket have the same schema as in this example.

## The Homework
Three months of fake ratings data are stored at s3://hinge-homework/director-data-engineering/ratings. We will be designing an ELT pipeline to make this stuff useful.

1. Spin up a database to store these ratings. As a reminder, Hinge uses Redshift, but feel free to use whatever is easiest for you.
2. Create a table in your DB for each file in the S3 bucket, with each table name equalling its corresponding file name.
3. Design a schema that easily enables analysis on this dataset. Write whatever code you need to write to actually transform the ratings data into your schema. 
4. Totally optional and not necessary extra credit: perform one of the below analyses on the dataset to show us how easy you made it, or show us something cool that you found yourself in the data.

In working on this homework, please work in a fork of this repo, and let us know when we can check out your code. We should be able to run your code with little difficulty. At Hinge, we use Bash and Python scripts to wrap these kinds of operations, but if you prefer something else, anything that we can run is fine. In the root directory of your fork, include a writeup justifying your ideas and methods.

As a reminder, a major part of this role is to be a technical lead. For this homework, we care more about elegance and robustness than completion.

Moreover, we are aware that there are less annoying ways to do this than to load into a bunch of different weirdly named tables and then transform. In real life, we do this process differently. However, this is not real life, this is just a homework.

#### Example questions for analysis, in order of increasing vagueness/realism:
* What is the average like rate (likes/(likes+skips))?
* What percentage of likes go unresponded to?
* What is the average reciprocation rate?
* What % of reports happen after two users connect as opposed to before?
* What is the average number of skips before a like?
* How often do people see duplicates potentials within a day?
* How often do people change their mind about the people they like?

#### Some considerations:
* A single rating_type can have multiple semantic meanings. For example, a rating_type of 3 either means “Remove this person from the list of people I can send likes to”, “Reject this person’s incoming like”, or “Remove the person from my list of matches”, depending on the preceding ratings between a pair of users. 
* Assume that every day, another file is added to this S3 bucket, and that you can’t predict the exact names of future files. How will you make sure that your load script catches these? How will you make sure that your transform script can find non-deterministically named tables?



