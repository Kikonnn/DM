import sqlite3

#This statement creates a connection labelled as conn.  This will be used throughout to ensure the consistency for when we start to query the database tables.
conn = sqlite3.connect('ecommerce.db')
cursor = conn.cursor()



# DM -- 用户表 (Users) - 包含客人和房东
CREATE TABLE IF NOT EXISTS Users (
    user_id TEXT PRIMARY KEY,          --  "HOST_123"  "GUEST_456"
    user_type TEXT NOT NULL CHECK (user_type IN ('Host', 'Guest')),
    email TEXT NOT NULL UNIQUE,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    phone_number TEXT NOT NULL,        --  "+44 7123 456789 ext. 123"
    country TEXT NOT NULL,
    registration_date DATE DEFAULT CURRENT_DATE,
    gender TEXT CHECK (gender IN ('Male', 'Female', 'Other'))
);

-- 房源表 (Listings)
CREATE TABLE IF NOT EXISTS Listings (
    listing_id TEXT PRIMARY KEY,
    host_id TEXT NOT NULL,
    title TEXT NOT NULL,
    description TEXT,
    property_type TEXT NOT NULL CHECK (property_type IN ('Apartment', 'House', 'Villa', 'Cabin')),
    country TEXT NOT NULL,
    street TEXT NOT NULL,
    house_number TEXT NOT NULL,        -- 可能包含字母（如 "12A"）
    price_per_night REAL NOT NULL CHECK (price_per_night > 0),
    availability_status TEXT NOT NULL CHECK (availability_status IN ('Available', 'Booked', 'Unlisted')),
    host_listing_count INTEGER DEFAULT 0,
    listing_popularity INTEGER DEFAULT 0,
    num_bedrooms INTEGER NOT NULL,
    num_bathrooms INTEGER NOT NULL,
    num_guests_max INTEGER NOT NULL,
    min_nights INTEGER NOT NULL CHECK (min_nights >= 1),
    amenities TEXT,                    -- 用逗号分隔的设施列表（如 "WiFi,Pool"）
    FOREIGN KEY (host_id) REFERENCES Users(user_id)
);

-- 预订表 (Bookings)
CREATE TABLE IF NOT EXISTS Bookings (
    booking_id TEXT PRIMARY KEY,
    customer_id TEXT NOT NULL,
    listing_id TEXT NOT NULL,
    price REAL NOT NULL CHECK (price > 0),
    check_in_date DATE NOT NULL,
    check_out_date DATE NOT NULL,
    status TEXT NOT NULL CHECK (status IN ('Confirmed', 'Cancelled', 'Pending')),
    booking_date DATE DEFAULT CURRENT_DATE,
    host_response_status TEXT CHECK (host_response_status IN ('Accepted', 'Rejected', 'Pending')),
    FOREIGN KEY (customer_id) REFERENCES Users(user_id),
    FOREIGN KEY (listing_id) REFERENCES Listings(listing_id),
    CHECK (check_out_date > check_in_date)  -- 确保退房日期晚于入住日期
);
