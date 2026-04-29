## Amazon Personalize

Amazon Personalize is a fully managed **real-time recommendation engine** — uses the same ML technology as Amazon.com without requiring ML expertise.

**Key Concepts:**
- **Dataset Group**: Container for all datasets, solutions, and campaigns for one use case.
- **Datasets**: **Interactions** (required — clicks, purchases, views; min 1,000 events & 25 users), **Users** (age, gender, tier), **Items** (category, price, genre).
- **Recipe**: Pre-built algorithm — `USER_PERSONALIZATION` (per-user history), `POPULAR_ITEMS` (trending), `PERSONALIZED_RANKING` (re-rank a list), `SIMILAR_ITEMS` (also-viewed/bought), `USER_SEGMENTATION` (group by interest).
- **Solution**: Trained model from applying a recipe to your dataset group.
- **Campaign**: Live endpoint — returns real-time recommendations via `GetRecommendations` API.
- **Batch**: Submit `userIds` from S3 → recommendations written back to S3.
- **Event Tracker**: Stream live events (clicks, purchases) to update recommendations in real time — no retraining needed.
- **Filters**: Exclude items from results using business rules (already-purchased, out-of-stock, specific categories).

**Use cases:** E-commerce recommendations, video/music suggestions, personalized news feeds, search result ranking.
