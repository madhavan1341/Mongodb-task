Zen class program using MongoDB:

1.Users

name (string)
email (string)
role (string)
joined_at (ISODate)

2.Codekata

name (string)
description (string)
difficulty (string)

3.Attendance

user_id (varchar or ObjectId, depending on the use case)
date (ISODate)
present (boolean)

4.Topics

name (string)
description (string)

5.Tasks

user_id (varchar or ObjectId, depending on the use case)
topic_id (varchar or ObjectId, depending on the use case)
description (string)
deadline (ISODate)

6.CompanyDrives

name (string)
date (ISODate)
description (string)

7.Mentors

name (string)
email (string)
expertise (string)

QUERIES:
1.Find all topics taught in October:
db.Topics.find({
  date: {
    $gte: ISODate("2023-10-01T00:00:00.000Z"),
    $lte: ISODate("2023-10-31T23:59:59.999Z")
  }
})

2.Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020

db.CompanyDrives.find({
  date: {
    $gte: ISODate("2020-10-15T00:00:00.000Z"),
    $lte: ISODate("2020-10-31T23:59:59.999Z")
  }
})

3.Find all the company drives and students who are appeared for the placement.
db.CompanyDrives.aggregate([
  {
    $lookup: {
      from: "Attendance",
      localField: "_id",
      foreignField: "drive_id",
      as: "attendance"
    }
  },
  {
    $unwind: "$attendance"
  },
  {
    $lookup: {
      from: "Users",
      localField: "attendance.user_id",
      foreignField: "_id",
      as: "student"
    }
  },
  {
    $unwind: "$student"
  },
  {
    $project: {
      "drive_name": "$name",
      "drive_date": "$date",
      "student_name": "$student.name",
      "student_email": "$student.email"
    }
   }
])
4.Find the number of problems solved by the user in codekata
db.Tasks.aggregate([
  {
    $group: {
      _id: "$user_id",
      problems_solved: { $sum: 1 }
    }
  },
  {
    $lookup: {
      from: "Users",
      localField: "_id",
      foreignField: "_id",
      as: "user"
    }
  },
  {
    $unwind: "$user"
  },
  {
    $project: {
      "user_name": "$user.name",
      "user_email": "$user.email",
      "problems_solved": 1
    }
  }
])
5.Find all the mentors with who has the mentee's count more than 15
db.Mentors.aggregate([
  {
    $lookup: {
      from: "Users",
      localField: "_id",
      foreignField: "mentor_id",
      as: "mentees"
    }
  },
  {
    $project: {
      name: 1,
      email: 1,
      expertise: 1,
      mentees_count: { $size: "$mentees" }
    }
  },
  {
    $match: {
      mentees_count: { $gt: 15 }
    }
  }
])
6.Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020

Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020

  





