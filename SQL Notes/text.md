### 1. **Database Design**:

You can create the following tables for your schema design. To show a screenshot of the table designs, you would need to use a database tool like MySQL Workbench or pgAdmin. I cannot generate screenshots directly, but here are the SQL queries to design the database:

```sql
CREATE TABLE users (
   userId INT AUTO_INCREMENT,
   firstName VARCHAR(50),
   lastName VARCHAR(50),
   email VARCHAR(50),
   PRIMARY KEY (userId)
);

CREATE TABLE posts (
  postId INT AUTO_INCREMENT,
  userId INT,
  text VARCHAR(255) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (postId),
  FOREIGN KEY (userId) REFERENCES users(userId) ON DELETE CASCADE
);

CREATE TABLE postsLikes (
  postLikeId INT AUTO_INCREMENT,
  postId INT,
  userId INT,
  PRIMARY KEY (postLikeId),
  FOREIGN KEY (postId) REFERENCES posts(postId) ON DELETE CASCADE,
  FOREIGN KEY (userId) REFERENCES users(userId) ON DELETE CASCADE
);

CREATE TABLE comments (
  commentId INT AUTO_INCREMENT,
  text VARCHAR(255) NOT NULL,
  postId INT,
  userId INT,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (commentId),
  FOREIGN KEY (postId) REFERENCES posts(postId) ON DELETE CASCADE,
  FOREIGN KEY (userId) REFERENCES users(userId) ON DELETE CASCADE
);

CREATE TABLE commentsLikes (
  commentsLikeId INT AUTO_INCREMENT,
  commentId INT,
  userId INT,
  PRIMARY KEY (commentsLikeId),
  FOREIGN KEY (commentId) REFERENCES comments(commentId) ON DELETE CASCADE,
  FOREIGN KEY (userId) REFERENCES users(userId) ON DELETE CASCADE
);

CREATE TABLE replies (
  replyId INT AUTO_INCREMENT,
  text VARCHAR(255) NOT NULL,
  commentId INT,
  userId INT,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (replyId),
  FOREIGN KEY (commentId) REFERENCES comments(commentId) ON DELETE CASCADE,
  FOREIGN KEY (userId) REFERENCES users(userId) ON DELETE CASCADE
);

CREATE TABLE repliesLikes (
  replyLikeId INT AUTO_INCREMENT,
  replyId INT,
  userId INT,
  PRIMARY KEY (replyLikeId),
  FOREIGN KEY (replyId) REFERENCES replies(replyId) ON DELETE CASCADE,
  FOREIGN KEY (userId) REFERENCES users(userId) ON DELETE CASCADE
);
```

### 2. **Select all the posts with their username, count of total reactions, and comments**:

```sql
SELECT 
  p.postId, 
  CONCAT(u.firstName, ' ', u.lastName) AS username, 
  COUNT(DISTINCT pl.postLikeId) AS total_reactions, 
  COUNT(DISTINCT c.commentId) AS total_comments
FROM posts p
JOIN users u ON p.userId = u.userId
LEFT JOIN postsLikes pl ON p.postId = pl.postId
LEFT JOIN comments c ON p.postId = c.postId
GROUP BY p.postId, u.firstName, u.lastName;
```

### 3. **Select all the comments of a post (given postId) with the author's name and reactions**:

```sql
SELECT 
  c.commentId, 
  CONCAT(u.firstName, ' ', u.lastName) AS author_name, 
  c.text, 
  COUNT(cl.commentsLikeId) AS total_reactions
FROM comments c
JOIN users u ON c.userId = u.userId
LEFT JOIN commentsLikes cl ON c.commentId = cl.commentId
WHERE c.postId = [given_postId] -- Replace [given_postId] with the post ID
GROUP BY c.commentId, u.firstName, u.lastName, c.text;
```

### 4. **Insert post, comment, and reply**:

- Insert a post:
```sql
INSERT INTO posts (userId, text) VALUES (1, 'This is a new post');
```

- Insert a comment:
```sql
INSERT INTO comments (userId, postId, text) VALUES (2, 1, 'This is a comment on post 1');
```

- Insert a reply:
```sql
INSERT INTO replies (userId, commentId, text) VALUES (3, 1, 'This is a reply to comment 1');
```

### 5. **Select users who made the most posts, sorted by post count**:

```sql
SELECT 
  u.userId, 
  CONCAT(u.firstName, ' ', u.lastName) AS username, 
  COUNT(p.postId) AS post_count
FROM users u
JOIN posts p ON u.userId = p.userId
GROUP BY u.userId, u.firstName, u.lastName
ORDER BY post_count DESC;
```

### 6. **Delete a post**:

To delete a post and its related comments, likes, and replies (due to `ON DELETE CASCADE`), you can run this:

```sql
DELETE FROM posts WHERE postId = [postId_to_delete];
```

Replace `[postId_to_delete]` with the actual post ID you want to delete. This will automatically delete all related data due to the cascade constraints.