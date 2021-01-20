CREATE TABLE "students" (
  "id" SERIAL,
  "name" TEXT,
  "rank" INTEGER
);

-- create table "table1" (
-- "emp_id" SERIAL, "emp_name" TEXT, "manager_id" integer,
--  "manager_name" text, "manager_phones" text);

CREATE TABLE "employees" (
  "id" SERIAL,
  "emp_name" TEXT,
  "manager_id" INTEGER
);

CREATE TABLE "employee_phones" (
  "emp_id" INTEGER,
  "phone_number" TEXT
);


-- 

CREATE TABLE "customers" (
  "id" SERIAL,
  "first_name" VARCHAR,
  "last_name" VARCHAR,
  "phone_number" VARCHAR
);

CREATE TABLE "customer_emails" (
  "customer_id" INTEGER,
  "email_address" VARCHAR
);

CREATE TABLE "rooms" (
  "id" SERIAL,
  "floor" SMALLINT,
  "room_no" SMALLINT,
  "area_sqft" SMALLINT
);

CREATE TABLE "reservations" (
  "id" SERIAL,
  "customer_id" INTEGER,
  "room_id" INTEGER,
  "check_in" DATE,
  "check_out" DATE
);

--
\d students
-- sees which types there are

ALTER TABLE "students" ALTER COLUMN "email_address" SET DATA TYPE VARCHAR;

ALTER TABLE "courses" ALTER COLUMN "rating" SET DATA TYPE REAL;

ALTER TABLE "registrations" ALTER COLUMN "student_id" SET DATA TYPE INTEGER;

ALTER TABLE "registrations" ALTER COLUMN "course_id" SET DATA TYPE INTEGER;


-- Migrate people
INSERT INTO "people" ("first_name", "last_name")
  SELECT "first_name", "last_name" FROM "denormalized_people";


-- Migrate people's emails using the correct ID
INSERT INTO "people_emails"
SELECT

  "p"."id",
  REGEXP_SPLIT_TO_TABLE("dn"."emails", ',')
FROM "denormalized_people" "dn"

JOIN "people" "p" ON (
  "dn"."first_name" = "p"."first_name"
  AND "dn"."last_name" = "p"."last_name"
);

-- Update the last_name column to be capitalized
UPDATE "people" SET "last_name" =
  SUBSTR("last_name", 1, 1) ||
  LOWER(SUBSTR("last_name", 2));
-- first character to first character
-- interval 


-- Change the born_ago column to date_of_birth
ALTER TABLE "people" ADD column "date_of_birth" DATE;

UPDATE "people" SET "date_of_birth" = 
  (CURRENT_TIMESTAMP - "born_ago"::INTERVAL)::DATE;

ALTER TABLE "people" DROP COLUMN "born_ago";



-- turn autocommit off
-- Do everything in a transaction
BEGIN;

-- Remove all users from New York and California
DELETE FROM "user_data" WHERE "state" IN ('NY', 'CA');


-- Split the name column in first_name and last_name
ALTER TABLE "user_data"
  ADD COLUMN "first_name" VARCHAR,
  ADD COLUMN "last_name" VARCHAR;

-- need to add the columns first
UPDATE "user_data" SET
  "first_name" = SPLIT_PART("name", ' ', 1),
  "last_name" = SPLIT_PART("name", ' ', 2);


ALTER TABLE "user_data" DROP COLUMN "name";


-- Change from state to state_id
CREATE TABLE "states" (
  "id" SMALLSERIAL,
  "state" CHAR(2)
);

INSERT INTO "states" ("state")
  SELECT DISTINCT "state" FROM "user_data";

ALTER TABLE "user_data" ADD COLUMN "state_id" SMALLINT;

UPDATE "user_data" SET "state_id" = (
  SELECT "s"."id"
  FROM "states" "s"
  WHERE "s"."state" = "user_data"."state"
);

ALTER TABLE "user_data" DROP COLUMN "state";


