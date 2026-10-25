# zebra-social
Zebra social media app

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Zebra Social</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f0f2f5;
  color: #202124;
}

header {
  background: #172554;
  color: white;
  padding: 16px;
  text-align: center;
}

header h1 {
  margin: 0;
}

header p {
  margin: 5px 0 0;
}

main {
  max-width: 520px;
  margin: auto;
  padding: 14px;
}

.card {
  background: white;
  border-radius: 12px;
  padding: 15px;
  margin-bottom: 14px;
  box-shadow: 0 2px 6px #0000000d;
}

input, textarea {
  width: 100%;
  padding: 12px;
  margin: 6px 0 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font: inherit;
}

textarea {
  resize: vertical;
}

button {
  border: 0;
  border-radius: 8px;
  padding: 10px 14px;
  margin: 3px;
  background: #1d4ed8;
  color: white;
  font-size: 14px;
  cursor: pointer;
}

button:hover {
  background: #1e40af;
}

.post {
  background: white;
  border-radius: 12px;
  padding: 15px;
  margin-bottom: 12px;
  overflow-wrap: anywhere;
}

.post-name {
  font-weight: bold;
  color: #1d4ed8;
}

.post-text {
  white-space: pre-wrap;
}

.muted {
  color: #666;
  font-size: 13px;
}

.comment {
  background: #f0f2f5;
  border-radius: 8px;
  padding: 8px;
  margin-top: 6px;
}

footer {
  text-align: center;
  padding: 18px;
  color: #666;
}
</style>
</head>

<body>

<header>
  <h1>🦓 Zebra</h1>
  <p>Share Your World</p>
</header>

<main>

  <section class="card">
    <h3>Create a Post</h3>

    <input
      id="username"
      placeholder="Enter your name"
      maxlength="50"
    >

    <textarea
      id="postText"
      rows="3"
      placeholder="What's on your mind?"
      maxlength="2000"
    ></textarea>

    <button onclick="addPost()">Publish Post</button>
  </section>

  <h3>News Feed</h3>
  <div id="feed"></div>

</main>

<footer>Zebra Social · Version 1.0</footer>

<script>
const STORAGE_KEY = "zebra_posts_v1";
let posts = [];

try {
  posts = JSON.parse(
    localStorage.getItem(STORAGE_KEY) || "[]"
  );

  if (!Array.isArray(posts)) {
    posts = [];
  }
} catch (error) {
  posts = [];
}

function savePosts() {
  try {
    localStorage.setItem(
      STORAGE_KEY,
      JSON.stringify(posts)
    );
  } catch (error) {
    alert("Unable to save posts in this browser.");
  }
}

function addPost() {
  const nameInput = document.getElementById("username");
  const textInput = document.getElementById("postText");

  const name = nameInput.value.trim() || "Zebra User";
  const text = textInput.value.trim();

  if (!text) {
    alert("Please write something first!");
    return;
  }

  posts.unshift({
    id: Date.now().toString() +
        Math.random().toString(16).slice(2),
    name: name,
    text: text,
    likes: 0,
    comments: []
  });

  savePosts();
  textInput.value = "";
  renderPosts();
}

function likePost(id) {
  const post = posts.find(p => p.id === id);
  if (!post) return;

  post.likes++;
  savePosts();
  renderPosts();
}

function commentPost(id) {
  const post = posts.find(p => p.id === id);
  if (!post) return;

  const comment = prompt("Write your comment:");

  if (comment === null || !comment.trim()) {
    return;
  }

  post.comments.push(comment.trim());
  savePosts();
  renderPosts();
}

function renderPosts() {
  const feed = document.getElementById("feed");
  feed.replaceChildren();

  if (posts.length === 0) {
    feed.textContent =
      "No posts yet. Be the first to share something!";
    return;
  }

  posts.forEach(p => {
    const article = document.createElement("article");
    article.className = "post";

    const name = document.createElement("div");
    name.className = "post-name";
    name.textContent = "🦓 " + p.name;

    const message = document.createElement("p");
    message.className = "post-text";
    message.textContent = p.text;

    const stats = document.createElement("div");
    stats.className = "muted";
    stats.textContent =
      "❤️ " + p.likes + " Likes · 💬 " +
      p.comments.length + " Comments";

    const likeButton = document.createElement("button");
    likeButton.textContent = "❤️ Like";
    likeButton.onclick = () => likePost(p.id);

    const commentButton = document.createElement("button");
    commentButton.textContent = "💬 Comment";
    commentButton.onclick = () => commentPost(p.id);

    article.append(
      name,
      message,
      stats,
      likeButton,
      commentButton
    );

    p.comments.forEach(c => {
      const comment = document.createElement("div");
      comment.className = "comment";
      comment.textContent = "💬 " + c;
      article.appendChild(comment);
    });

    feed.appendChild(article);
  });
}

renderPosts();
</script>

</body>
</html>
