# Instagram User Analytics: A Comprehensive Data Analysis Project Report

## Project Name
Instagram User Analytics

![Image](https://github.com/kumarChaitanya1208/Data-Analytics-SQL-Project-Instagram-User-Analytics/blob/main/Poster%20Instagram%20user%20analysis.jpg)

---

## Problem Statement
In the dynamic social media landscape, Instagram faces challenges in understanding user behavior comprehensively. Key problems include identifying loyal and disengaged users, optimizing content strategy, timing marketing campaigns effectively, and maintaining platform integrity by detecting fraudulent accounts. Addressing these issues is crucial for sustained growth, enhanced user experience, and informed strategic investments.

---

## Project Goal
The primary goal of this project is to provide actionable intelligence to Instagram's product, marketing, and investor teams by analyzing user data. This includes:
* Identifying key user segments (e.g., loyal, inactive).
* Optimizing marketing and ad campaign effectiveness.
* Informing content strategy and feature development.
* Assessing and maintaining platform integrity against bots and fake accounts.
* Ultimately, supporting data-driven decision-making for business growth and user engagement.

---

## Preparation

### Data Source and Schema
The data for this analysis was provided by the Instagram product team and is stored in a MySQL database named `ig_clone`. The database comprises seven interconnected tables, collectively capturing a wide spectrum of user activities.

#### Database Schema Overview
The `ig_clone` database schema includes the following tables:

* **`users`**: Contains fundamental information about registered Instagram users, including `id`, `username`, and `created_at` (registration timestamp).
* **`photos`**: Stores details of user-uploaded photos, linking them back to the `users` table via `user_id`. It includes `id`, `image_url`, and `user_id`.
* **`comments`**: Records all comments made on photos, linking to both `users` and `photos` tables. It contains `id`, `comment_text`, `user_id`, and `photo_id`.
* **`likes`**: Tracks which users have liked which photos, serving as a many-to-many relationship table between `users` and `photos`. It includes `user_id` and `photo_id`.
* **`follows`**: Documents the follower-following relationships between users, with `follower_id` and `followee_id` both referencing the `users` table.
* **`tags`**: Lists all unique hashtags available on the platform, including `id` and `tag_name`.
* **`photo_tags`**: A junction table linking `photos` with `tags`, indicating which tags were used on which photos. It contains `photo_id` and `tag_id`.

### Data Collection and Preprocessing
The data was provided in a structured, tabular format, indicative of a clean dataset. Initial examination confirmed the absence of duplicates and missing values, thus eliminating the need for extensive data cleaning procedures. This allowed for immediate progression to the analytical phase.

---

## Data Processing and Methodology

The analysis was performed using **SQL (Structured Query Language)** executed via **MySQL Workbench 8.0 CE**. MySQL Workbench 8.0.35 served as the primary querying and administration tool, facilitating efficient interaction with the MySQL database. The approach involved a series of targeted queries designed to address specific business questions.

Techniques employed included:

* **Joins**: Utilizing `LEFT JOIN`, `INNER JOIN` to combine data from multiple tables based on common attributes, enabling comprehensive data integration for relational analysis.
* **Aggregation Functions**: Employing `COUNT()`, `AVG()` to summarize data (e.g., total number of likes, average number of posts per user), providing a high-level overview of key metrics.
* **Filtering**: Using `WHERE` and `HAVING` clauses to narrow down results based on specific conditions (e.g., users without photos, photos with maximum likes), ensuring focus on relevant data subsets.
* **Sorting**: Applying `ORDER BY` to arrange results for easier interpretation (e.g., oldest to newest users, highest to lowest engagement), making patterns and trends readily apparent.
* **Subqueries and Derived Tables**: Constructing nested queries to perform multi-step calculations, such as identifying users who liked every photo or calculating average posts per user, allowing for complex analytical scenarios.

This methodology ensured a systematic and robust approach to extracting meaningful insights from the Instagram user data.

---

## Detailed Analysis and Findings

This section presents the detailed analysis performed in response to questions posed by Instagram's marketing and investor teams. Each query is accompanied by an explanation, the derived insight, and its business implications.

### 5.1 Marketing Analysis

#### 5.1.1 Loyal User Reward

* **Objective**: To identify Instagram's most loyal users, defined as those who have been on the platform for the longest duration.
* **SQL Query**:
    ```sql
    SELECT *
    FROM users
    ORDER BY created_at ASC
    LIMIT 5;
    ```
* **Explanation**: This query selects all columns from the `users` table, orders the results by the `created_at` timestamp in ascending order, and then limits the output to the top 5 entries, effectively identifying the earliest registered users.
* **Key Insight**: The five oldest users on Instagram were identified based on their creation date.
* **Business Impact**: The identification of these users allows the marketing team to initiate a targeted loyalty program, offering exclusive rewards or early access to new features. This fosters a sense of appreciation and can strengthen community bonds.

#### 5.1.2 Inactive User Engagement

* **Objective**: To pinpoint users who have registered but have never contributed content (i.e., posted a single photo).
* **SQL Query**:
    ```sql
    SELECT users.id, username
    FROM users
    LEFT JOIN photos ON users.id = photos.user_id
    WHERE user_id IS NULL
    ORDER BY id ASC;
    ```
* **Explanation**: This query performs a `LEFT JOIN` between the `users` and `photos` tables. By checking for `NULL` values in the `user_id` column from the `photos` table (which would only occur if a user has no corresponding photo entries), it identifies users who have not posted.
* **Key Insight**: A significant number of users have registered but never posted content.
* **Business Impact**: A list of inactive users enables the marketing team to design specific re-engagement campaigns, such as promotional emails featuring popular content, new feature announcements, or tutorials to encourage their first post. This can reactivate dormant accounts and increase overall content generation.

#### 5.1.3 Contest Winner Declaration

* **Objective**: To determine the winner of a contest based on the photo receiving the highest number of likes.
* **SQL Query**:
    ```sql
    SELECT
        photo_id,
        users.id AS user_id,
        username,
        COUNT(photo_id) AS No_of_Likes
    FROM likes
    LEFT JOIN photos ON likes.photo_id = photos.id
    LEFT JOIN users ON photos.user_id = users.id
    GROUP BY photo_id
    ORDER BY No_of_Likes DESC
    LIMIT 1;
    ```
* **Explanation**: This query joins `likes`, `photos`, and `users` tables to link likes to specific photos and their respective users. It then groups the results by `photo_id`, counts the likes for each photo, and orders them in descending order to find the photo with the maximum likes.
* **Key Insight**: The user with the most likes on a single photo was identified.
* **Business Impact**: Identifying the contest winner allows for prompt prize distribution and public recognition, boosting user participation in future contests and encouraging content creation. It also provides insights into what type of content resonates most with the audience.

#### 5.1.4 Hashtag Research

* **Objective**: To identify the top five most frequently used hashtags on the platform for partner brand guidance.
* **SQL Query**:
    ```sql
    SELECT
        tag_id, tag_name, COUNT(photo_id) AS No_of_time_used
    FROM photo_tags
    INNER JOIN tags ON photo_tags.tag_id = tags.id
    GROUP BY tag_id
    ORDER BY No_of_time_used DESC
    LIMIT 5;
    ```
* **Explanation**: This query joins `photo_tags` with `tags` to get the tag names associated with photos. It then groups by `tag_id`, counts how many photos used each tag, and orders the results to find the top 5 most used tags.
* **Key Insight**: The top five most commonly used hashtags on the platform were identified (e.g., 'smile', 'beach', 'party', 'fun', 'concert').
* **Business Impact**: Knowledge of the most popular hashtags is invaluable for marketing and content strategy. Partner brands can leverage these hashtags to increase the visibility and reach of their posts, while Instagram can use this information to curate trending content or inform feature development around tagging.

#### 5.1.5 Ad Campaign Launch Optimization

* **Objective**: To determine the most effective day of the week for launching ad campaigns by identifying when most new users register.
* **SQL Query**:
    ```sql
    SELECT
        DAYNAME(created_at) AS days, COUNT(*) AS User_Registration_Count
    FROM users
    GROUP BY days
    ORDER BY User_Registration_Count DESC;
    ```
* **Explanation**: This query extracts the day name from the `created_at` timestamp in the `users` table, groups the registrations by day, and counts the total registrations for each day. The results are ordered to show the days with the highest registration counts.
* **Key Insight**: Sundays and Thursdays experience the highest user registration rates.
* **Business Impact**: This direct insight allows the marketing team to strategically schedule ad campaigns on these specific days, maximizing their potential reach and conversion rates, leading to more efficient ad spending.

### 5.2 Investor Metrics

#### 5.2.1 User Engagement Assessment

* **Objective**: To evaluate overall user activity by calculating the average number of posts per user and the ratio of total photos to total users.
* **SQL Queries**:
    ```sql
    -- Average number of posts per user
    SELECT AVG(post_count) AS average_posts_per_user
    FROM (
        SELECT userid, COUNT(photoid) AS post_count
        FROM (
            SELECT users.id AS userid, photos.id AS photoid
            FROM users
            LEFT JOIN photos ON photos.user_id = users.id
        ) AS photo_users
        GROUP BY userid
    ) AS post_counts;

    -- Total photos divided by total users
    SELECT
        COUNT(photoid) AS total_photos,
        COUNT(DISTINCT userid) AS total_users,
        COUNT(photoid) / COUNT(DISTINCT userid) AS photos_per_user
    FROM (
        SELECT users.id AS userid, photos.id AS photoid
        FROM users
        LEFT JOIN photos ON photos.user_id = users.id
    ) AS photo_users;
    ```
* **Explanation**: Both queries use subqueries to first create a derived table of `userid` and `photoid` by joining `users` and `photos`. The first query then groups this by `userid` to get individual post counts and calculates the average. The second query simply counts total photos and distinct users from this derived table and calculates their ratio.
* **Key Insight**: The average number of posts per user and the overall photos-per-user ratio were calculated, providing a quantitative measure of content contribution.
* **Business Impact**: These metrics provide investors with a clear picture of content contribution and overall platform activity. A healthy average of posts per user and a favorable photo-to-user ratio indicate an engaged user base, which is a crucial indicator of platform vitality and growth potential.

#### 5.2.2 Bots & Fake Accounts Detection

* **Objective**: To identify potential bot or fake accounts by detecting users who have liked every single photo on the platform.
* **SQL Query**:
    ```sql
    SELECT
        user_id,
        COUNT(user_id) AS No_of_post_likes,
        username,
        users.created_at
    FROM likes
    INNER JOIN users ON likes.user_id = users.id
    GROUP BY user_id
    HAVING No_of_post_likes = (SELECT COUNT(*) FROM photos);
    ```
* **Explanation**: This query first determines the total number of photos on the platform using a subquery. It then joins the `likes` and `users` tables, groups by `user_id`, and counts the total likes made by each user. The `HAVING` clause filters for users whose like count exactly matches the total number of photos, indicating they have liked every single post.
* **Key Insight**: Users exhibiting behavior consistent with bot activity (liking every single photo) were identified.
* **Business Impact**: The detection of users who have liked every photo is a strong indicator of bot activity, as this behavior is highly uncharacteristic of genuine human users. This analysis provides critical data for investors regarding platform integrity and user authenticity. Addressing these fake accounts improves the overall quality of engagement and trust in the Instagram ecosystem.

---

## Recommendation

Based on the detailed analysis, the following recommendations are proposed to enhance Instagram's strategic operations and user experience:

1.  **Implement Loyalty Programs**: Develop and launch specific reward programs for the identified loyal users. This could include exclusive badges, early access to beta features, or special recognition within the community to foster continued engagement and advocacy.
2.  **Targeted Re-engagement Campaigns**: Initiate automated email or in-app notification campaigns for inactive users. These campaigns should feature personalized content suggestions, tutorials on how to create compelling posts, or incentives (e.g., "Your first post gets boosted!") to encourage their initial contribution.
3.  **Content Strategy Refinement**: Utilize insights from popular hashtags and top-liked photos to guide content creation workshops for users and inform official Instagram content curation. This ensures that the platform promotes content types that resonate most with its audience.
4.  **Optimized Ad Scheduling**: Strictly adhere to ad campaign launches on Sundays and Thursdays to capitalize on peak user registration periods. A/B testing can further refine the exact timing on these days for maximum impact and efficient ad spending.
5.  **Enhanced Bot Detection and Mitigation**: Integrate more sophisticated algorithms for continuous bot detection and implement proactive measures to remove or flag suspicious accounts. Regularly auditing accounts exhibiting anomalous behavior (like liking every post) is crucial for maintaining platform authenticity and user trust.
6.  **Further Engagement Metrics Exploration**: Conduct deeper dives into additional metrics such as comment frequency per user, average session duration, and follower growth rate. This will provide a more holistic view of user engagement beyond just posts and likes.

---

## Action

The recommendations outlined above translate into the following actionable steps for Instagram's teams:

* **Marketing Team**:
    * **Loyalty**: Design and execute a "Veteran User" recognition program for the top 5 oldest users, potentially including a personalized message from the Instagram CEO or a special in-app badge.
    * **Re-engagement**: Develop a drip email campaign for identified inactive users, with a clear call to action to make their first post, highlighting trending content.
    * **Ad Campaigns**: Update the ad scheduling calendar to prioritize campaign launches on Sundays and Thursdays, backed by performance monitoring.
* **Product Team**:
    * **Content**: Develop a feature that suggests popular hashtags to users when they are composing a post, based on real-time trends and the project's findings.
    * **User Experience**: Investigate UX improvements to simplify the first-time posting experience for new users, potentially including guided tutorials.
* **Data Science/Trust & Safety Team**:
    * **Bot Detection**: Implement the identified SQL query (or a refined version) as an automated script to regularly scan for users exhibiting bot-like behavior. Develop a protocol for reviewing and, if necessary, disabling such accounts.
    * **Metrics**: Begin exploration into more granular engagement metrics as recommended, integrating these into existing dashboards.
* **Leadership/Investors**:
    * Review monthly reports on loyal user engagement, inactive user re-engagement rates, bot account mitigation, and the impact of optimized ad scheduling to track progress against strategic goals.

---

## Conclusion & Reflection

This Instagram User Analytics project successfully leveraged SQL and MySQL Workbench to transform raw data into actionable business intelligence. By meticulously analyzing user behavior across various dimensions, crucial insights were provided to both marketing and investor stakeholders. The project highlights the power of data-driven decision-making in optimizing marketing strategies, enhancing user engagement, and safeguarding platform integrity. As an IITian, the structured approach taken, akin to solving complex engineering problems, proved highly effective in breaking down the challenge and deriving clear, impactful solutions.

### Key Achievements

* Successfully identified and categorized users for targeted marketing initiatives (loyal, inactive).
* Determined optimal timing for advertising campaigns based on user registration patterns.
* Provided insights into content performance and popular trends through hashtag analysis and contest winner identification.
* Developed metrics for user engagement and detected potential bot accounts, addressing key investor concerns.
* Enhanced practical data analysis skills using industry-standard tools and techniques.

This project underscores the continuous need for data analysis in understanding and responding to user dynamics, ensuring Instagram's continued growth and success.

---

## Acknowledgements

I would like to express my gratitude to the Instagram product team for providing the dataset that made this analysis possible. Their support was invaluable in completing this project.
