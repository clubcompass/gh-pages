{% include navigation.html %}

## Individual tech requirements (`5/5`)

- [x] Database with multiple tables (`1/1`)

9 Tables in total with numerous table relations (one to many, many to many)

```prisma
model Club {
  id              String               @id @default(cuid())
  name            String?              @unique
  slug            String?              @unique
  description     String?
  email           String?
  meetingDate     String?
  location        String?
  approval        Boolean?             @default(false)
  status          ClubStatus           @default(DRAFT)
  availability    ClubAvailability     @default(OPEN)
  teacherId       String?
  createdAt       DateTime             @default(now())
  updatedAt       DateTime             @updatedAt
  teacher         User?                @relation("advisor", fields: [teacherId], references: [id])
  applicationInfo ClubApplicationInfo?
  invites         Invite[]
  links           Link[]
  roles           Role[]
  tags            Tag[]
  editors         User[]               @relation("editors")
  members         User[]               @relation("members")
}

model Link {
  id        String   @id @default(cuid())
  clubId    String
  name      String
  link      String
  type      LinkType @default(WEBSITE)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  club      Club     @relation(fields: [clubId], references: [id], onDelete: Cascade)
}

model ClubApplicationInfo {
  id                                  String              @id @default(cuid())
  clubId                              String?             @unique
  purpose                             String?
  membershipRequirements              String?
  dutiesOfMembers                     String?
  titlesAndDutiesOfOfficers           String?
  selectionOfOfficers                 String?
  officerMinimumGPA                   Float?
  percentAttendanceForOfficialMeeting Int?
  percentAttendanceToApproveDecision  Int?
  createdAt                           DateTime            @default(now())
  updatedAt                           DateTime            @updatedAt
  club                                Club?               @relation(fields: [clubId], references: [id])
  projectedExpenses                   ProjectedExpenses[]
  projectedRevenue                    ProjectedRevenue[]
}

model Tag {
  id              String   @id @default(cuid())
  name            String   @unique
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
  clubs           Club[]
  interestedUsers User[]
}

model User {
  id                String      @id @default(cuid())
  ccid              String      @unique
  studentId         String      @unique
  firstname         String
  lastname          String
  email             String      @unique
  emailVerified     Boolean     @default(true)
  verificationToken String?     @unique
  active            Boolean     @default(false)
  password          String
  grade             Grade
  type              AccountType @default(STUDENT)
  createdAt         DateTime    @default(now())
  updatedAt         DateTime    @updatedAt
  advisor           Club[]      @relation("advisor")
  invites           Invite[]
  roles             Role[]
  interests         Tag[]
  canEdit           Club[]      @relation("editors")
  clubs             Club[]      @relation("members")
}

model Invite {
  id        String       @id @default(cuid())
  status    InviteStatus @default(PENDING)
  type      InviteType   @default(INCOMING)
  clubId    String
  userId    String
  createdAt DateTime     @default(now())
  updatedAt DateTime     @updatedAt
  club      Club         @relation(fields: [clubId], references: [id], onDelete: Cascade)
  user      User         @relation(fields: [userId], references: [id], onDelete: Cascade)
  roles     Role[]
}

model Role {
  id          String   @id @default(cuid())
  name        String
  color       String
  description String
  clubId      String
  type        RoleType @default(MEMBER)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  club        Club     @relation(fields: [clubId], references: [id], onDelete: Cascade)
  invites     Invite[]
  users       User[]
}

model ProjectedRevenue {
  id        String               @id @default(cuid())
  clubId    String?
  name      String?
  amount    Float?
  date      String?
  createdAt DateTime             @default(now())
  updatedAt DateTime             @updatedAt
  club      ClubApplicationInfo? @relation(fields: [clubId], references: [id])
}

model ProjectedExpenses {
  id        String               @id @default(cuid())
  clubId    String?
  name      String?
  amount    Float?
  date      String?
  createdAt DateTime             @default(now())
  updatedAt DateTime             @updatedAt
  club      ClubApplicationInfo? @relation(fields: [clubId], references: [id])
}

enum Grade {
  FRESHMAN
  SOPHMORE
  JUNIOR
  SENIOR
  TEACHER
}

enum RoleType {
  MEMBER
  LEADER
}

enum InviteStatus {
  PENDING
  DECLINED
  ACCEPTED
}

enum InviteType {
  OUTGOING
  INCOMING
}

enum ClubAvailability {
  OPEN
  INVITE_ONLY
  CLOSED
}

enum AccountType {
  STUDENT
  TEACHER
  ASB
  ADMIN
}

enum ClubStatus {
  DRAFT
  REVIEW
  APPROVED
  DECLINED
}

enum LinkType {
  EMAIL
  TWITTER
  INSTAGRAM
  DISCORD
  YOUTUBE
  FACEBOOK
  REMIND
  SNAPCHAT
  WEBSITE
}
```

- [x] Authentication and authorization controls (`1/1`)

Handled through the login/register system as well as role based access controls.

- Login flow: When a user logs in their credentials are checked against the database. The first thing that is checked is the username, if the username doesn't exist in the database then the user is informed through a client form error. If the username does exist, then the password is checked. If the decrypted on record password matches the inputted password, then the user is redirected to the dashboard. If the password is incorrect, then the user will be informed through a client form error.
- Register flow: user adds all necessary information to create an account through the register onboarding then a user is created if all servers side data validation (username exists, student id exists, no numbers in name, etc...) passes. The user is then programmatically logged in to have an HTTP only cookie set in their browser for handling the user session. The user is then sent an email with a magic (jwt) link to activate their account. Accounts are inactive and unapproved when first created.
- In browser http only cookie is used to make authorized requests.

- [x] Meaningful Capture (`1/1`)

Many relations connecting associated tables

- [x] Creativity and applications of new Tech feature features to Sponsor Project (`1/1`)

Project is completely unique and solves a niche problem in high schools around the globe. New tech includes a heavy usable of `Typescript`, `GraphQL`, and `NextJS`.

- [x] Not adding distracting features for Sponsor theme, ie non-applicable games, non-applicable fun zones (`1/1`)

No areas of project have distracting features.
