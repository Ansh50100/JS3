// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract College {
    struct Student {
        uint256 id;
        string name;
        string[] courses;
    }
    struct Course {
        string name;
        uint8 credits;
    }
    address public admin;
    uint256 public studentCount;
    mapping(uint256 => Student) public students;
    mapping(string => Course) public courses;
    modifier onlyAdmin() {
        require(msg.sender == admin, "You are not the admin");
        _;
    }
    constructor() {
        admin = msg.sender;
        studentCount = 0;
    }
    function addStudent(string memory _name) public onlyAdmin {
        students[studentCount] = Student(studentCount, _name, new string[](0));
        studentCount++;
    }
    function addCourse(string memory _courseName, uint8 _credits) public onlyAdmin {
        require(bytes(_courseName).length > 0, "Course name cannot be empty");
        courses[_courseName] = Course(_courseName, _credits);
    }
    function enrollCourse(uint256 _studentId, string memory _courseName) public onlyAdmin {
        require(_studentId < studentCount, "Student does not exist");
        require(bytes(courses[_courseName].name).length > 0, "Course does not exist");
        Student storage student = students[_studentId];
        student.courses.push(_courseName);
    }
    function assertStudentExists(uint256 _studentId) public view {
        assert(_studentId < studentCount);
    }
    function revertIfCourseDoesNotExist(string memory _courseName) public view {
        if (bytes(courses[_courseName].name).length == 0) {
            revert("Course does not exist");
        }
    }
}
