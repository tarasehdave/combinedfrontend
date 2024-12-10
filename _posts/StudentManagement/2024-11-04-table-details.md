---
layout: post
title: Table Details
type: issues
permalink: /tabledetails
comments: false
---
<style>
  h2 {
      color: white;
  }
  #student-cards-container {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 20px;
      margin-top: 20px;
      justify-content: center;
  }
  .student-card {
      background-color: #fff;
      border: 1px solid #ddd;
      border-radius: 5px;
      padding: 20px;
      width: 280px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
  }
  .student-card h3 {
      margin: 10px 0;
      font-size: 20px;
      color: black;
  }
  .student-card p {
      margin: 5px 0;
      font-size: 16px;
      color: black;
  }
  .student-image {
      width: 100px;
      height: 100px;
      border-radius: 50%;
      margin-bottom: 10px;
  }
  .delete-button, .add-task-button {
      margin-top: 10px;
      padding: 8px 12px;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
  }
  .delete-button {
      background-color: #ff4d4d;
  }
  .add-task-button {
      background-color: #28a745;
  }
  .create-button {
      margin: 20px auto;
      padding: 10px 30px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      display: block;
      font-size: 16px;
      font-weight: bold;
  }
</style>
<body>
  <h2 id="page-title">Students in Table</h2>
  <div id="student-cards-container"></div>
  <button class="create-button" onclick="createStudent()">Create Student</button>

  <script>
    document.addEventListener("DOMContentLoaded", function() {
      const urlParams = new URLSearchParams(window.location.search);
      const tableNumber = urlParams.get('table');

      if (tableNumber) {
        fetch("http://localhost:8085/api/students/find-team", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            course: "CSA",
            trimester: 1,
            period: 3,
            table: parseInt(tableNumber)
          })
        })
        .then(response => {
          if (!response.ok) throw new Error("Network response was not ok");
          return response.json();
        })
        .then(data => {
          const container = document.getElementById("student-cards-container");
          container.innerHTML = "";

          // Set the project name in the title using the first student in the list (assuming same project for the table)
          if (data.length > 0) {
            document.getElementById("page-title").textContent = `Project: ${data[0].project} - Students in Table ${tableNumber}`;
          }

          data.forEach(student => {
            const card = document.createElement("div");
            card.className = "student-card";
            
            // Fetch GitHub profile picture
            fetch(`https://api.github.com/users/${student.username}`)
              .then(response => response.json())
              .then(githubData => {
                const imageUrl = githubData.avatar_url || "default-image-url.jpg";
                card.innerHTML = `
                  <img src="${imageUrl}" alt="${student.username}'s Profile Picture" class="student-image">
                  <h3>${student.name}</h3>
                  <p>Username: ${student.username}</p>
                  <p>Table Number: ${student.tableNumber}</p>
                  <p>Course: ${student.course}</p>
                  <p>Trimester: ${student.trimester}</p>
                  <p>Period: ${student.period}</p>
                  <p>Tasks: ${student.tasks.join(", ")}</p>
                  <button class="add-task-button" onclick="addTask('${student.username}')">Add Task</button>
                  <button class="delete-button" onclick="deleteStudent('${student.username}')">Delete</button>
                `;
              })
              .catch(error => {
                console.error("GitHub profile fetch error:", error);
                card.innerHTML = `
                  <img src="default-image-url.jpg" alt="Default Profile Picture" class="student-image">
                  <h3>${student.name}</h3>
                  <p>Username: ${student.username}</p>
                  <p>Table Number: ${student.tableNumber}</p>
                  <p>Course: ${student.course}</p>
                  <p>Trimester: ${student.trimester}</p>
                  <p>Period: ${student.period}</p>
                  <p>Tasks: ${student.tasks.join(", ")}</p>
                  <button class="add-task-button" onclick="addTask('${student.username}')">Add Task</button>
                  <button class="delete-button" onclick="deleteStudent('${student.username}')">Delete</button>
                `;
              });
            container.appendChild(card);
          });
        })
        .catch(error => console.error("There was a problem with the fetch operation:", error));
      } else {
        document.getElementById("student-cards-container").innerHTML = "<p>No table selected.</p>";
      }
    });
function addTask(username) {
      const newTask = prompt("Enter a new task:");
      if (newTask) {
        fetch("http://localhost:8181/api/students/update-tasks", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            username: username,
            tasks: [newTask]
          })
        })
        .then(response => {
          if (!response.ok) throw new Error("Failed to add task");
          return response.json();
        })
        .then(student => {
          alert("Task added successfully!");
          location.reload();
        })
        .catch(error => console.error("There was a problem with the add task operation:", error));
      } else {
        alert("Task cannot be empty.");
      }
    }
        function createStudent() {
      const name = prompt("Enter student name:");
      const username = prompt("Enter student username:");
      const tableNumber = prompt("Enter table number:");
      const course = "CSA";
      const trimester = 1;
      const period = 3;
      const tasks = []; // Initial empty tasks

      if (name && username && tableNumber) {
        fetch("http://127.0.0.1:8181/api/students/create", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            name: name,
            username: username,
            tableNumber: parseInt(tableNumber),
            course: course,
            trimester: trimester,
            period: period,
            tasks: tasks
          })
        })
        .then(response => {
          if (!response.ok) throw new Error("Failed to create student");
          return response.json();
        })
        .then(student => {
          alert("Student created successfully!");
          location.reload();
        })
        .catch(error => console.error("There was a problem with the create operation:", error));
      } else {
        alert("Please fill in all fields to create a student.");
      }
    }

    function deleteStudent(username) {
      fetch(`http://127.0.0.1:8181/api/students/delete?username=${encodeURIComponent(username)}`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        mode: "cors"
      })
      .then(response => {
        if (!response.ok) throw new Error("Failed to delete student with username: " + username);
        return response.text();
      })
      .then(message => {
        console.log(message);
        alert(message);
        location.reload();
      })
      .catch(error => console.error("There was a problem with the delete operation:", error));
    }
  </script>
</body>