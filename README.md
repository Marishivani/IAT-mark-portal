
const readline = require("readline");

// Input/Output setup
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

// Helper function to take input
function ask(question) {
  return new Promise(resolve => rl.question(question, ans => resolve(ans)));
}

// Student class
class Student {
  constructor(name, roll, subjects) {
    this.name = name;
    this.roll = roll;
    this.subjects = subjects; // {subjectCode: mark}
    this.failedSubjects = [];
  }

  calculateFailures(passMark = 35) {
    this.failedSubjects = Object.keys(this.subjects).filter(
      sub => this.subjects[sub] < passMark
    );
    return this.failedSubjects.length;
  }

  totalMarks() {
    return Object.values(this.subjects).reduce((a, b) => a + b, 0);
  }

  reportCard() {
    console.log("\n---------------- Report Card ----------------");
    console.log(`Name     : ${this.name}`);
    console.log(`Roll No  : ${this.roll}`);
    console.log("---------------------------------------------");
    for (let [sub, mark] of Object.entries(this.subjects)) {
      let status = mark >= 25 ? "Pass" : "Fail";
      console.log(`${sub} : ${mark}  --> ${status}`);
    }
    console.log("---------------------------------------------");
    console.log(`Total Marks      : ${this.totalMarks()}`);
    console.log(
      `Failed Subjects  : ${this.failedSubjects.length} (${
        this.failedSubjects.length > 0
          ? this.failedSubjects.join(", ")
          : "None"
      })`
    );
    console.log("---------------------------------------------");
    console.log("Parent's Signature: ____________");
    console.log("HOD's Signature   : ____________");
    console.log("---------------------------------------------");
  }
}

async function main() {
  let students = [];
  let failureCount = {}; // subject-wise failures

  let n = parseInt(await ask("Enter number of students: "));

  for (let i = 0; i < n; i++) {
    console.log(`\n--- Enter details for Student ${i + 1} ---`);
    let name = await ask("Enter student name: ");
    let roll = await ask("Enter roll number: ");
    let subCount = parseInt(await ask("Enter number of subjects: "));

    let subjects = {};
    for (let j = 0; j < subCount; j++) {
      let code = await ask(`Enter subject code ${j + 1}: `);
      let mark = parseInt(await ask(`Enter mark for ${code}: `));
      subjects[code] = mark;
    }

    let student = new Student(name, roll, subjects);
    student.calculateFailures();
    students.push(student);

    // Update subject-wise failures
    student.failedSubjects.forEach(sub => {
      failureCount[sub] = (failureCount[sub] || 0) + 1;
    });
  }

  // Show subject-wise failure counts
  console.log("\n========== Subject-wise Failure Count ==========");
  if (Object.keys(failureCount).length === 0) {
    console.log("No failures in any subject!");
  } else {
    for (let [sub, count] of Object.entries(failureCount)) {
      console.log(`${sub} : ${count} failures`);
    }
  }

  // Search for a student
  console.log("\n========== Search Student Report ==========");
  let searchRoll = await ask("Enter roll number to search: ");
  let found = students.find(s => s.roll === searchRoll);

  if (found) {
    found.reportCard();
  } else {
    console.log("Student not found!");
  }

  rl.close();
}

// Run program
main();
