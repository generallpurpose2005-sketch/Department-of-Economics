# Department-of-Economics
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Economics Wall Magazine | Nalbari College</title>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  background: #f4f4f4;
}

header {
  background: #002147;
  color: white;
  padding: 20px;
  text-align: center;
}

nav a {
  color: white;
  margin: 10px;
  text-decoration: none;
  font-weight: bold;
}

section {
  background: white;
  margin: 20px;
  padding: 20px;
  border-radius: 5px;
}

#welcome h3 {
  color: #002147;
}

#visitor-box {
  background: #002147;
  color: white;
  padding: 10px;
  margin: 20px;
  width: fit-content;
  border-radius: 5px;
}

.post, .comment, .pdf-card {
  background: #f4f4f4;
  padding: 15px;
  margin-top: 10px;
  border-radius: 5px;
}

.pdf-card {
  border-left: 5px solid #002147;
}

textarea {
  width: 100%;
  height: 80px;
}

button {
  background: #002147;
  color: white;
  padding: 8px 15px;
  border: none;
  cursor: pointer;
  border-radius: 4px;
}

footer {
  background: #002147;
  color: white;
  text-align: center;
  padding: 15px;
  margin-top: 30px;
}
</style>
</head>

<body>

<header>
  <h1>Economics Wall Magazine</h1>
  <h3>Nalbari College</h3>
  <nav>
    <a href="#welcome">Home</a>
    <a href="#pdf-section">PDF Archive</a>
    <a href="#comments">Comments</a>
  </nav>
</header>

<div id="visitor-box">
  👁️ Total Visitors: <span id="visitorCount">0</span>
</div>

<section id="welcome">
  <h2>Welcome to the Wall Magazine</h2>
  <h3>Department of Economics, Nalbari College</h3>
  <p>
    The Wall Magazine of the Department of Economics, Nalbari College
    is an academic and creative platform for students and faculty.
    It showcases articles, poems, economic ideas, research insights,
    achievements, and departmental activities.
  </p>
</section>

<section id="posts">
  <h2>Latest Posts</h2>
</section>

<section id="pdf-section">
  <h2>📄 Wall Magazine PDF Archive</h2>
  <div id="pdfList"></div>
</section>

<section id="comments">
  <h2>Student & Visitor Comments</h2>

  <input type="text" id="name" placeholder="Your Name"><br><br>
  <textarea id="comment" placeholder="Write your comment here..."></textarea><br><br>
  <button onclick="postComment()">Submit Comment</button>

  <div id="commentList"></div>
</section>

<footer>
  © Department of Economics, Nalbari College <br>
  Official Wall Magazine
</footer>

<script>
// 🔹 FIREBASE CONFIG (REPLACE WITH YOUR OWN)
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT.firebaseio.com",
  projectId: "YOUR_PROJECT"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// 🔹 VISITOR COUNTER
const visitRef = db.ref("visitors");
visitRef.transaction(v => (v || 0) + 1);
visitRef.on("value", s => {
  document.getElementById("visitorCount").innerText = s.val();
});

// 🔹 LOAD POSTS
db.ref("posts").on("value", snap => {
  const box = document.getElementById("posts");
  box.innerHTML = "<h2>Latest Posts</h2>";
  snap.forEach(d => {
    const p = d.val();
    box.innerHTML += `
      <div class="post">
        <h3>${p.title}</h3>
        <p>${p.content}</p>
        <small>By ${p.author}</small>
      </div>`;
  });
});

// 🔹 LOAD PDFs
db.ref("pdfs").on("value", snap => {
  const pdfBox = document.getElementById("pdfList");
  pdfBox.innerHTML = "";
  snap.forEach(d => {
    const pdf = d.val();
    pdfBox.innerHTML += `
      <div class="pdf-card">
        <h3>${pdf.title}</h3>
        <p>Year: ${pdf.year}</p>
        <a href="${pdf.url}" target="_blank">📥 View / Download</a>
      </div>`;
  });
});

// 🔹 COMMENTS
function postComment() {
  const name = document.getElementById("name").value;
  const text = document.getElementById("comment").value;

  if (!name || !text) {
    alert("Please fill all fields");
    return;
  }

  db.ref("comments").push({
    name: name,
    comment: text,
    time: new Date().toLocaleString()
  });

  document.getElementById("name").value = "";
  document.getElementById("comment").value = "";
}

db.ref("comments").on("value", snap => {
  const list = document.getElementById("commentList");
  list.innerHTML = "";
  snap.forEach(d => {
    const c = d.val();
    list.innerHTML += `
      <div class="comment">
        <strong>${c.name}</strong><br>
        ${c.comment}<br>
        <small>${c.time}</small>
      </div>`;
  });
});
</script>

</body>
</html>
