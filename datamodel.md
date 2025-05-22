# 🧠 TrendTap Data Model (ERD)

## 1. Users

| Field           | Type        | Notes                          |
|----------------|-------------|--------------------------------|
| user_id        | UUID        | Primary Key                    |
| email          | String      | Unique                         |
| password_hash  | String      | Encrypted login credential     |
| display_name   | String      | Public name                    |
| profile_photo_url | String   | Optional profile photo         |
| brand_voice    | Text        | JSON or prompt-based structure |
| niches         | Array[String] | User-selected focus areas    |
| plan_type      | Enum        | 'free', 'pro', 'agency'        |

---

## 2. ConnectedAccounts

| Field           | Type        | Notes                                |
|----------------|-------------|--------------------------------------|
| account_id     | UUID        | Primary Key                          |
| user_id        | UUID        | Foreign Key → Users                  |
| platform       | Enum        | 'instagram', 'tiktok', 'youtube', etc. |
| auth_token     | String      | OAuth token (encrypted)              |
| account_handle | String      | Displayed to user                    |
| last_sync_time | Timestamp   | For API sync tracking                |

---

## 3. Trends

| Field         | Type         | Notes                                      |
|--------------|--------------|--------------------------------------------|
| trend_id     | UUID         | Primary Key                                |
| platform     | String       | Platform source (e.g., 'tiktok')           |
| niche        | String       | Industry/category                          |
| type         | Enum         | 'audio', 'hashtag', 'news', 'format'       |
| title        | String       | Trend name or summary                      |
| metadata     | JSON         | Popularity score, tags, usage, etc.        |
| detected_at  | Timestamp    | Last scraped/fetched time                  |

---

## 4. NewsArticles

| Field         | Type         | Notes                           |
|--------------|--------------|---------------------------------|
| article_id   | UUID         | Primary Key                     |
| headline     | String       | News headline                   |
| url          | String       | Source link                     |
| published_at | Timestamp    | Original publish date           |
| topic_tags   | Array[String]| Tags for filtering & matching   |

---

## 5. ContentIdeas

| Field      | Type         | Notes                                             |
|-----------|--------------|---------------------------------------------------|
| idea_id   | UUID          | Primary Key                                       |
| user_id   | UUID          | Foreign Key → Users                               |
| trend_id  | UUID          | Foreign Key → Trends                              |
| platform  | String        | Suggested platform for content                    |
| hook      | Text          | First line or pattern interrupt                   |
| caption   | Text          | CTA and styled message                           |
| format    | Enum          | 'reel', 'carousel', 'tweet', 'short', etc.        |
| status    | Enum          | 'suggested', 'accepted', 'scheduled', 'posted'    |

---

## 6. MediaAssets

| Field        | Type        | Notes                                      |
|-------------|-------------|--------------------------------------------|
| asset_id    | UUID        | Primary Key                                |
| user_id     | UUID        | Foreign Key → Users                        |
| url         | String      | S3/GCS media file URL                      |
| type        | Enum        | 'image', 'video', 'audio'                  |
| tags        | Array[String]| Used for search and categorization         |
| uploaded_at | Timestamp   | Upload timestamp                           |

---

## 7. ScheduledPosts

| Field          | Type         | Notes                                          |
|---------------|--------------|------------------------------------------------|
| post_id       | UUID         | Primary Key                                    |
| user_id       | UUID         | Foreign Key → Users                            |
| platform      | String       | Platform to publish on                         |
| account_id    | UUID         | Foreign Key → ConnectedAccounts                |
| scheduled_time| Timestamp    | UTC time to publish                            |
| media_ids     | Array[UUID]  | References MediaAssets                         |
| caption       | Text         | Final caption for the post                     |
| status        | Enum         | 'scheduled', 'posted', 'failed'                |

---

## 8. EngagementStats

| Field           | Type      | Notes                                |
|----------------|-----------|--------------------------------------|
| stat_id        | UUID      | Primary Key                          |
| post_id        | UUID      | Foreign Key → ScheduledPosts         |
| likes          | Integer   | Number of likes                      |
| comments       | Integer   | Number of comments                   |
| shares         | Integer   | Number of shares                     |
| views          | Integer   | Number of views                      |
| engagement_rate| Float     | Calculated engagement (likes/views)  |
| collected_at   | Timestamp | Last synced                          |

---

## 🔄 Relationships Summary

- `Users` → `ConnectedAccounts` (1:M)
- `Users` → `ContentIdeas` (1:M)
- `Users` → `MediaAssets` (1:M)
- `Users` → `ScheduledPosts` (1:M)
- `Trends` → `ContentIdeas` (1:M)
- `ScheduledPosts` → `EngagementStats` (1:1)
- `MediaAssets` → `ScheduledPosts` (M:N via array or join table if scaling)

---

