# BloodVault

A blood bank web app where donors register, log their donations, and anyone signed in can search for donors by blood group and city.

![Node.js](https://img.shields.io/badge/Node.js-339933?logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white)
![EJS](https://img.shields.io/badge/EJS-B4CA65?logo=ejs&logoColor=black)
![Materialize](https://img.shields.io/badge/Materialize-EE6E73?logo=materialdesign&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)

**Live demo:** https://bloodvault.onrender.com

## About

BloodVault is a server-rendered Node.js and Express app backed by MongoDB. Donors register with their name, blood group, city, phone number and address, then record how many pints of blood they have donated. Registered users can browse the donor bank, filter it by blood group, Rh factor and city, and call a donor straight from the results. The landing page also has general information about blood donation eligibility and blood type compatibility.

## Features

- **Donor registration** with Mongoose schema validation (blood group format, 10-digit phone number, non-negative amount). Phone numbers are unique; registering with an existing number signs that donor back in.
- **Cookie-based session**: the donor's phone number is stored in a signed cookie (signed with `KEY`) that lasts 2 days.
- **Donation log**: signed-in donors can add pints to their running total and see when they last donated.
- **Donor search** on `/bank` by blood group (A, B, O, AB), Rh factor (+/-) and city, with case-insensitive matching.
- **Results** are sorted by amount donated (highest first), 18 per page via the `page` query parameter, with a `tel:` link to call each donor.
- **Seed script** (`npm run populate`) that fills the database with random sample donors from the JSON files in `utils/`.

## Tech Stack

| Layer    | Technology                                       |
| -------- | ------------------------------------------------ |
| Runtime  | Node.js                                          |
| Server   | Express 4, body-parser, cookie-parser, dotenv    |
| Database | MongoDB with Mongoose 5                          |
| Views    | EJS templates and static HTML                    |
| Frontend | Materialize CSS, jQuery                          |

## Project Structure

```
BloodVault/
├── index.js            # Express app: config, routes, server start
├── models/
│   └── user.js         # Mongoose donor schema and validation
├── views/              # EJS templates
│   ├── bank.ejs        # Donor search and results
│   ├── donate.ejs      # Donor dashboard and donation form
│   └── footer.ejs
├── public/
│   ├── index.html      # Landing page
│   ├── register.html   # Registration form
│   ├── css/ js/ img/ json/   # Static assets (served from the site root)
├── utils/
│   ├── populator.js    # Seeds the DB with random donors
│   └── *.json          # Sample names, cities, addresses, phones, blood groups
├── .envexample         # Example environment variables
└── package.json
```

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) and npm
- A MongoDB instance (local or hosted, for example MongoDB Atlas)

### Installation

```bash
git clone https://github.com/AryanDeswal/BloodVault.git
cd BloodVault
npm install
```

### Environment Variables

Copy `.envexample` to `.env` and fill in your own values:

```bash
cp .envexample .env
```

| Variable | Purpose                                                                                  |
| -------- | ---------------------------------------------------------------------------------------- |
| `DB_URL` | MongoDB connection string. Defaults to `mongodb://localhost:27017/blookBankDB` if unset. |
| `KEY`    | Secret used to sign the session cookie. Set this to a long random string.                |
| `PORT`   | Port the server listens on. Defaults to `3000`.                                          |

### Run

```bash
npm start
```

Then open http://localhost:3000.

To seed the database with sample donors:

```bash
npm run populate
```

The seed script starts the app, inserts the sample donors once the database connects, and keeps running. Stop it with `Ctrl+C` when it is done.

## Routes

| Method | Route       | Description                                                                                      |
| ------ | ----------- | ------------------------------------------------------------------------------------------------ |
| GET    | `/`         | Landing page with blood donation information                                                     |
| GET    | `/register` | Donor registration form                                                                          |
| POST   | `/register` | Creates a donor (or signs in an existing one by phone), sets the cookie, redirects to `/donate`  |
| GET    | `/donate`   | Donor dashboard with total donated and last donation date (requires sign-in)                    |
| POST   | `/donate`   | Adds `amount` pints to the signed-in donor's total                                               |
| GET    | `/bank`     | Donor search. Query params: `blood`, `rh`, `city`, `page` (requires sign-in)                    |
| GET    | `/logout`   | Clears the session cookie and redirects to `/`                                                   |

## License

Released under the [MIT License](LICENSE).
