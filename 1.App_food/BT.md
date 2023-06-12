USE App_food;

CREATE TABLE userInfor (
    userId INT NOT NULL,
    fullName VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL,
    password VARCHAR(100) NOT NULL,
    PRIMARY KEY(userId)
);

CREATE TABLE restaurant (
    resId INT NOT NULL,
    resName VARCHAR(100) NOT NULL,
    image VARCHAR(100) NOT NULL,
    descRes VARCHAR(100) NOT NULL,
    PRIMARY KEY(resId)
);

CREATE TABLE rateRes (
    userId INT NOT NULL,
    resId INT NOT NULL,
    amount INT NOT NULL,
    dateRate DATETIME NOT NULL,
    PRIMARY KEY(userId, resId),
    CONSTRAINT FK_rateRes_userId FOREIGN KEY(userId) REFERENCES userInfor(userId),
    CONSTRAINT FK_rateRes_resId FOREIGN KEY(resId) REFERENCES restaurant(resId)
);

CREATE TABLE likeRes (
    userId INT NOT NULL,
    resId INT NOT NULL,
    dateLike DATETIME NOT NULL,
    PRIMARY KEY(userId, resId),
    CONSTRAINT FK_likeRes_userId FOREIGN KEY(userId) REFERENCES userInfor(userId),
    CONSTRAINT FK_likeRes_resId FOREIGN KEY(resId) REFERENCES restaurant(resId)
);

CREATE TABLE food (
    foodId INT NOT NULL,
    foodName VARCHAR(100) NOT NULL,
    image VARCHAR(100) NOT NULL,
    price FLOAT NOT NULL,
    descFood VARCHAR(100) NOT NULL,
    typeId INT NOT NULL,
    PRIMARY KEY(foodId),
    CONSTRAINT FK_food_typeId FOREIGN KEY(typeId) REFERENCES foodType(typeId)
);

CREATE TABLE orderInfor (
    userId INT NOT NULL,
    foodId INT NOT NULL,
    amount INT NOT NULL,
    code VARCHAR(100) NOT NULL,
    arrSubId VARCHAR(100) NOT NULL,
    PRIMARY KEY(userId, foodId),
    CONSTRAINT FK_orderInfor_userId FOREIGN KEY(userId) REFERENCES userInfor(userId),
    CONSTRAINT FK_orderInfor_foodId FOREIGN KEY(foodId) REFERENCES food(foodId)
);

CREATE TABLE subFood (
    subId INT NOT NULL,
    subName VARCHAR(100) NOT NULL,
    subPrice FLOAT NOT NULL,
    foodId INT NOT NULL,
    PRIMARY KEY(subId),
    CONSTRAINT FK_subFood_foodId FOREIGN KEY(foodId) REFERENCES food(foodId)
);

CREATE TABLE foodType (
    typeId INT NOT NULL,
    typeName VARCHAR(100),
    PRIMARY KEY(typeId)
);