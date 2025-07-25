<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Study Buddy</title>
  <link href="https://fonts.googleapis.com/css2?family=Quicksand:wght@600&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      margin: 0;
      padding: 0;
      background: linear-gradient(135deg, #ffffff, #ffe3ec, #d0f4de, #c6e2ff);
      background-size: 400% 400%;
      animation: gradientBG 15s ease infinite;
      color: #333;
    }

    @keyframes gradientBG {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }

    header {
      background-color: #5c6ac4;
      color: white;
      padding: 15px 20px;
      text-align: left;
    }

    header h1 {
      margin: 0;
      font-size: 2.3em;
      font-weight: 600;
      font-family: 'Quicksand', sans-serif;
    }

    main {
      padding: 20px;
    }

    .subject-btn {
      padding: 10px 15px;
      border: none;
      margin: 5px;
      border-radius: 8px;
      background-color: #6ac46a;
      color: white;
      cursor: pointer;
      font-size: 1em;
    }

    .subject-btn:hover {
      background-color: #4a9e4a;
    }

    #subject-container {
      margin-bottom: 20px;
    }

    .task {
      background-color: #ffffff;
      padding: 10px;
      margin: 10px 0;
      border-radius: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }

    .completed {
      text-decoration: line-through;
      color: #999;
    }

    input[type="text"] {
      padding: 12px;
      width: 75%;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 1em;
    }

    button {
      padding: 12px 18px;
      border: none;
      border-radius: 8px;
      background-color: #5c6ac4;
      color: white;
      cursor: pointer;
      font-size: 1em;
    }

    button:hover {
      background-color: #3f4aad;
    }

    #notification {
      display: none;
      margin-top: 15px;
      padding: 10px 15px;
      border-radius: 8px;
      background-color: #5c6ac4;
      color: white;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <header>
    <h1>Study Buddy</h1>
  </header>
  <main>
    <div id="subject-container">
      <input type="text" id="subjectInput" placeholder="Enter a subject...">
      <button onclick="addSubject()">Add Subject</button>
    </div>

    <div id="subjectList"></div>
    <div id="notification"></div>
  </main>

  <script>
    let subjects = JSON.parse(localStorage.getItem('subjects')) || {};

    function saveSubjects() {
      localStorage.setItem('subjects', JSON.stringify(subjects));
    }

    function showNotification(message) {
      const notif = document.getElementById("notification");
      notif.textContent = message;
      notif.style.display = "block";
      clearTimeout(notif.timeout);
      notif.timeout = setTimeout(() => {
        notif.style.display = "none";
      }, 3000);
    }

    function addSubject() {
      const subjectInput = document.getElementById("subjectInput");
      const subjectName = subjectInput.value.trim();
      if (!subjectName) return;

      if (!subjects[subjectName]) {
        subjects[subjectName] = [];
        saveSubjects();
        renderSubjects();
        showNotification(`Subject "${subjectName}" added!`);
      } else {
        showNotification(`Subject "${subjectName}" already exists.`);
      }

      subjectInput.value = '';
    }

    function renderSubjects() {
      const subjectList = document.getElementById("subjectList");
      subjectList.innerHTML = '';

      for (let subject in subjects) {
        const btn = document.createElement("button");
        btn.className = "subject-btn";
        btn.textContent = subject;
        btn.onclick = () => openSubject(subject);
        subjectList.appendChild(btn);
      }
    }

    function openSubject(subjectName) {
      const main = document.querySelector("main");
      main.innerHTML = `
        <button onclick="window.location.reload()">⬅ Back</button>
        <h2>${subjectName}</h2>
        <input type="text" id="taskInput" placeholder="Add a task for ${subjectName}...">
        <button onclick="addTask('${subjectName}')">Add Task</button>
        <div id="taskList"></div>
        <div id="notification"></div>
      `;
      renderTasks(subjectName);
    }

    function addTask(subjectName) {
      const input = document.getElementById("taskInput");
      const text = input.value.trim();
      if (!text) return;

      subjects[subjectName].push({ text, completed: false });
      saveSubjects();
      renderTasks(subjectName);
      showNotification("Task added!");
      input.value = '';
    }

    function renderTasks(subjectName) {
      const taskList = document.getElementById("taskList");
      taskList.innerHTML = '';

      subjects[subjectName].forEach((task, index) => {
        const div = document.createElement("div");
        div.className = "task";

        const label = document.createElement("label");
        const checkbox = document.createElement("input");
        checkbox.type = "checkbox";
        checkbox.checked = task.completed;
        checkbox.onchange = () => toggleTask(subjectName, index);

        const span = document.createElement("span");
        span.textContent = task.text;
        if (task.completed) span.classList.add("completed");

        label.appendChild(checkbox);
        label.appendChild(span);

        const del = document.createElement("button");
        del.textContent = "Delete";
        del.onclick = () => deleteTask(subjectName, index);

        div.appendChild(label);
        div.appendChild(del);
        taskList.appendChild(div);
      });
    }

    function toggleTask(subjectName, index) {
      subjects[subjectName][index].completed = !subjects[subjectName][index].completed;
      saveSubjects();
      renderTasks(subjectName);
    }

    function deleteTask(subjectName, index) {
      subjects[subjectName].splice(index, 1);
      saveSubjects();
      renderTasks(subjectName);
    }

    renderSubjects();
  </script>
</body>
</html>
