


## Stream IP Camera to VLC player
- URL= `rtsp://{username}:{password}@{ip}:554/stream1`

## Convert stream to HLS
Execute FFMPEG command
`.\server\libs\ffmpeg.exe -i rtsp://admin:Ab@12345@192.168.23.159:554/stream1 -fflags flush_packets -max_delay 5 -flags -global_header -hls_time 5 -hls_list_size 3 -vcodec copy -y .\assets\ipcam\index.mp4`

video files (`index.m3u8 *.ts`)


## Install the packages 
- Open new terminal tab
- Go inside server folder
- Run `npm install`



## Test hls file in browser
- Visit [`cookpete.com/react-player`](https://cookpete.com/react-player).
- Input the m3u8 url [http://localhost:4000/index.m3u8] and press `Load` 
## Run react client
- Open new terminal tab
- Go inside `client\hls-client` folder
- Run `npm install`
- Run `npm start`


# DB Command 
 psql -h localhost -U postgres -d Test -f IOTSDB_dump.sql

 DB
 Cameras Table:

camera_id (Primary Key)
camera_name
location_id (Foreign Key to Locations Table)
ip_address
event_sensor (Boolean to indicate if an event is triggered)
Locations Table:

location_id (Primary Key)
location_name
Events Table:

event_id (Primary Key)
camera_id (Foreign Key to Cameras Table)
timestamp
event_type (e.g., motion detection, door access, etc.)
Streams Table:

stream_id (Primary Key)
camera_id (Foreign Key to Cameras Table)
start_time (when streaming started)
end_time (when streaming ended, or NULL if it's currently streaming)
Recordings Table:

recording_id (Primary Key)
camera_id (Foreign Key to Cameras Table)
start_time
end_time
file_location
// Run knex 
npx knex migrate:latest 
// npx knex migrate:up 
create migration
npx knex migrate:make name
npx knex migrate:rollback
HIKVISION
Note:
To enable Hik-Connect service, you need to create a verification code or change the verification code.

Verification Code:123456
psql -U postgres -d IOT_NEW -a -f IOTS_dump.sql
DB
CREATE TABLE "Organization" (
  "organization_id" integer PRIMARY KEY,
  "name" varchar UNIQUE,
  "country" varchar,
  "created_at" timestamp
);

CREATE TABLE "User" (
  "user_id" integer PRIMARY KEY,
  "first_name" varchar,
  "last_name" varchar,
  "email" varchar UNIQUE,
  "password" varchar,
  "is_verified" boolean,
  "created_at" timestamp,
  "user_type" varchar,
  "organization_id" integer
);

CREATE TABLE "Site" (
  "site_id" integer PRIMARY KEY,
  "name" varchar,
  "city" varchar,
  "address" varchar,
  "contact_person_name" varchar,
  "contact_1" bigint,
  "contact_2" bigint,
  "email" varchar,
  "created_at" timestamp,
  "organization_id" integer
);

CREATE TABLE Location (
  location_id integer PRIMARY KEY,
  name varchar
  floor jsonb
  site_id integer
  created_at timestamp
);

CREATE TABLE "RegisteredCamera" (
  "camera_id" integer PRIMARY KEY,
  "name" varchar,
  "ip_address" varchar,
  "event_type" varchar,
  "event_sensor" varchar,
  "device_interlocking" varchar,
  "created_at" timestamp,
  "location_id" integer
);

CREATE TABLE "Events" (
  "event_id" integer PRIMARY KEY,
  "timestamp" timestamp,
  "event_type" varchar,
  "camera_id" integer
);

CREATE TABLE "Stream" (
  "stream_id" integer PRIMARY KEY,
  "start_time" timestamp,
  "end_time" timestamp,
  "camera_id" integer
);

CREATE TABLE "Recording" (
  "recording_id" integer PRIMARY KEY,
  "start_time" timestamp,
  "end_time" timestamp,
  "file_location" varchar,
  "camera_id" integer,
  "stream_id" integer
);

CREATE TABLE "NVR" (
  "nvr_id" integer PRIMARY KEY,
  "name" varchar,
  "organization_id" integer
);

CREATE TABLE "NVR_Cameras" (
  "nvr_id" integer,
  "camera_id" integer
);

ALTER TABLE "User" ADD FOREIGN KEY ("organization_id") REFERENCES "Organization" ("organization_id");

ALTER TABLE "Site" ADD FOREIGN KEY ("organization_id") REFERENCES "Organization" ("organization_id");

ALTER TABLE "Location" ADD FOREIGN KEY ("site_id") REFERENCES "Site" ("site_id");

ALTER TABLE "RegisteredCamera" ADD FOREIGN KEY ("location_id") REFERENCES "Location" ("location_id");

ALTER TABLE "Events" ADD FOREIGN KEY ("camera_id") REFERENCES "RegisteredCamera" ("camera_id");

ALTER TABLE "Stream" ADD FOREIGN KEY ("camera_id") REFERENCES "RegisteredCamera" ("camera_id");

ALTER TABLE "Recording" ADD FOREIGN KEY ("camera_id") REFERENCES "RegisteredCamera" ("camera_id");

ALTER TABLE "Recording" ADD FOREIGN KEY ("stream_id") REFERENCES "Stream" ("stream_id");

ALTER TABLE "NVR" ADD FOREIGN KEY ("organization_id") REFERENCES "Organization" ("organization_id");

ALTER TABLE "NVR_Cameras" ADD FOREIGN KEY ("nvr_id") REFERENCES "NVR" ("nvr_id");

ALTER TABLE "NVR_Cameras" ADD FOREIGN KEY ("camera_id") REFERENCES "RegisteredCamera" ("camera_id");

I start a big  project that is related to Video surveillance  i made my  db  in PostgreSQL and backend in Node Js and frontend in React js 
step 1 : Database 
I have done my  DB  
here is  my  relational Database 
(
Table Organization {
  organization_id integer [primary key]
  organization_name varchar [unique]
  country varchar
  created_at timestamp
}
Table Site {
 site_id integer [PRIMARY KEY]
  site_name varchar
  city varchar
  address varchar
  contact_person_name varchar
  contact_1 bigint
  contact_2 bigint
  email varchar
  created_at timestamp
  organization_id integer  [ref: > Organization.organization_id] 
}

Table User {
  user_id integer [primary key]
  first_name varchar
  last_name varchar
  email varchar [unique]
  password varchar
  is_verified boolean
  created_at timestamp
  user_type varchar
  user_status bit
  site_id integer [ref: > Site.site_id] 
}

Table NVR {
  nvr_id integer [primary key]
  name varchar
  site_id integer [ref: > Site.site_id]
  created_at timestamp
  model varchar
  serial_number varchar
  firmware_version varchar
  storage_capacity integer
  is_online boolean
  ip_address varchar 

}


 TABLE Floor_manger {
  floor_id integer [primary key]
  name varchar
  floor jsonb
 site_id integer [ref: > Site.site_id]
  created_at timestamp
}

Table Camera {
  camera_id integer [primary key]
  name varchar
  ip_address varchar
  event_type varchar
  event_sensor varchar
  device_interlocking varchar
  nvr_id integer [ref: > NVR.nvr_id]
  created_at timestamp
  status bit
}

Table Stream {
  stream_id integer [primary key]
  start_time timestamp
  end_time timestamp
  camera_id integer [ref: > Camera.camera_id]
  stream_type varchar 
}

Table Recording {
  recording_id integer [primary key]
  start_time timestamp
  end_time timestamp
  file_location varchar
  recording_type varchar
  stream_id integer [ref: > Stream.stream_id]
   camera_id integer [ref: > Camera.camera_id]
}

Table Events {
  event_id integer [primary key]
  timestamp timestamp
  event_type varchar
   event_details varchar 
  camera_id integer [ref: > Camera.camera_id]
}

)
 backend and frontend are pending 
i take streaming from ffmpeg library  
Project Descriptions:
I have an Organization Like Cloudtek is my  organization
and here is 3 sites  CSV ,CNS and CodeShop 
each  site contain 2 NVRS and each Site contain 20 users  and each NVR  contain 20 cameras 
each camera contain multiple events when event happen start streaming and  and when stream start on specifice camera than recording i also start and save on my  db with  file path 
when user what to see any  event or recording of specifice time and select sites and select NVR and then select camera 1 and 9 then user is able to  see  stream of that cameras.
please under stand my  scenarios and make both frontend and backend and also check  my  DB any  mistake then correct me .Also give me textual  detail.