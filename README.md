<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Daily Task List</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f4;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    h1 {
      margin-top: 30px;
      color: #333;
    }
    #login-section, #signup-section, #task-section {
      display: none;
      width: 100%;
      max-width: 600px;
      margin: 20px;
    }
    #task-form {
      margin: 20px 0;
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      justify-content: center;
    }
    input[type="text"], input[type="date"], input[type="password"] {
      padding: 10px;
      width: 200px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    #add-task-btn, #login-btn, #signup-btn, #logout-btn {
      padding: 10px 15px;
      background-color: #28a745;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    ul {
      list-style-type: none;
      padding: 0;
    }
    li {
      background: white;
      margin: 10px;
      padding: 10px;
      border-radius: 5px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
    }
    .task-info {
      flex-grow: 1;
    }
    .edit-btn, .delete-btn {
      margin-left: 5px;
      background: #007bff;
      color: white;
      border: none;
      padding: 5px 10px;
      border-radius: 3px;
      cursor: pointer;
    }
    .delete-btn {
      background: #dc3545;
    }
    footer {
      margin-top: 40px;
      padding: 20px;
      text-align: center;
      color: #666;
      font-size: 0.9em;
    }
  </style>
</head>
<body>
  <h1>My Daily Tasks</h1>

  <div id="signup-section">
    <input type="text" id="signup-username" placeholder="Create username" required>
    <input type="password" id="signup-password" placeholder="Create password" required>
    <button id="signup-btn">Sign Up</button>
    <p>Already have an account? <a href="#" onclick="showLogin()">Login here</a></p>
  </div>

  <div id="login-section">
    <input type="text" id="username-input" placeholder="Enter username" required>
    <input type="password" id="password-input" placeholder="Enter password" required>
    <button id="login-btn">Login</button>
    <p>Don't have an account? <a href="#" onclick="showSignup()">Sign up here</a></p>
  </div>

  <div id="task-section">
    <h3 id="welcome-message"></h3>
    <button id="logout-btn">Logout</button>
    <form id="task-form">
      <input type="text" id="task-input" placeholder="Enter task..." required>
      <input type="date" id="deadline-input" required>
      <input type="text" id="category-input" placeholder="Category..." required>
      <button type="submit" id="add-task-btn">Add Task</button>
    </form>
    <ul id="task-list"></ul>
  </div>

  <footer>
    &copy; 2025 My Daily Task List | Created by You
  </footer>

  <script>
    const loginSection = document.getElementById('login-section');
    const signupSection = document.getElementById('signup-section');
    const taskSection = document.getElementById('task-section');
    const loginBtn = document.getElementById('login-btn');
    const signupBtn = document.getElementById('signup-btn');
    const logoutBtn = document.getElementById('logout-btn');
    const usernameInput = document.getElementById('username-input');
    const passwordInput = document.getElementById('password-input');
    const signupUsername = document.getElementById('signup-username');
    const signupPassword = document.getElementById('signup-password');
    const welcomeMsg = document.getElementById('welcome-message');

    const taskForm = document.getElementById('task-form');
    const taskInput = document.getElementById('task-input');
    const deadlineInput = document.getElementById('deadline-input');
    const categoryInput = document.getElementById('category-input');
    const taskList = document.getElementById('task-list');

    window.onload = function () {
      const user = JSON.parse(localStorage.getItem('user'));
      if (user) {
        showTaskSection(user.username);
        loadTasks();
      } else {
        showLogin();
      }
    };

    function showSignup() {
      signupSection.style.display = 'block';
      loginSection.style.display = 'none';
      taskSection.style.display = 'none';
    }

    function showLogin() {
      signupSection.style.display = 'none';
      loginSection.style.display = 'block';
      taskSection.style.display = 'none';
    }

    function showTaskSection(username) {
      signupSection.style.display = 'none';
      loginSection.style.display = 'none';
      taskSection.style.display = 'block';
      welcomeMsg.textContent = `Welcome, ${username}!`;
    }

    signupBtn.onclick = function () {
      const username = signupUsername.value.trim();
      const password = signupPassword.value.trim();
      if (username && password) {
        localStorage.setItem(`user_${username}`, JSON.stringify({ username, password }));
        alert('Account created! Please login.');
        showLogin();
      }
    };

    loginBtn.onclick = function () {
      const username = usernameInput.value.trim();
      const password = passwordInput.value.trim();
      const savedUser = JSON.parse(localStorage.getItem(`user_${username}`));
      if (savedUser && savedUser.password === password) {
        localStorage.setItem('user', JSON.stringify(savedUser));
        showTaskSection(savedUser.username);
        loadTasks();
      } else {
        alert('Invalid credentials!');
      }
    };

    logoutBtn.onclick = function () {
      localStorage.removeItem('user');
      location.reload();
    };

    function loadTasks() {
      const tasks = JSON.parse(localStorage.getItem('tasks')) || [];
      taskList.innerHTML = '';
      tasks.forEach(task => addTaskToList(task.text, task.deadline, task.category));
    }

    taskForm.addEventListener('submit', function(e) {
      e.preventDefault();
      const taskText = taskInput.value.trim();
      const deadline = deadlineInput.value;
      const category = categoryInput.value.trim();
      if (!taskText || !deadline || !category) return;
      addTaskToList(taskText, deadline, category);
      saveTasks();
      taskInput.value = '';
      deadlineInput.value = '';
      categoryInput.value = '';
    });

    function addTaskToList(text, deadline, category) {
      const li = document.createElement('li');
      const taskInfo = document.createElement('div');
      taskInfo.classList.add('task-info');
      taskInfo.innerHTML = `<strong>${text}</strong><br><small>Deadline: ${deadline}</small><br><small>Category: ${category}</small>`;

      const editBtn = document.createElement('button');
      editBtn.textContent = 'Edit';
      editBtn.classList.add('edit-btn');
      editBtn.onclick = function () {
        const newText = prompt("Edit task:", text);
        const newDeadline = prompt("Edit deadline:", deadline);
        const newCategory = prompt("Edit category:", category);
        if (newText && newDeadline && newCategory) {
          taskInfo.innerHTML = `<strong>${newText}</strong><br><small>Deadline: ${newDeadline}</small><br><small>Category: ${newCategory}</small>`;
          saveTasks();
        }
      };

      const deleteBtn = document.createElement('button');
      deleteBtn.textContent = 'Delete';
      deleteBtn.classList.add('delete-btn');
      deleteBtn.onclick = function() {
        taskList.removeChild(li);
        saveTasks();
      };

      li.appendChild(taskInfo);
      li.appendChild(editBtn);
      li.appendChild(deleteBtn);
      taskList.appendChild(li);
    }

    function saveTasks() {
      const tasks = [];
      document.querySelectorAll('#task-list li .task-info').forEach(taskDiv => {
        const [titleLine, deadlineLine, categoryLine] = taskDiv.innerHTML.split('<br>');
        const text = titleLine.replace(/<[^>]+>/g, '');
        const deadline = deadlineLine.replace(/<[^>]+>/g, '').replace('Deadline: ', '');
        const category = categoryLine.replace(/<[^>]+>/g, '').replace('Category: ', '');
        tasks.push({ text, deadline, category });
      });
      localStorage.setItem('tasks', JSON.stringify(tasks));
    }
  </script>
</body>
</html>
