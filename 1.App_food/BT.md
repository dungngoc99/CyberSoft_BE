CREATE TABLE user_infor (
user_id INT NOT NULL,
full_name VARCHAR(100) NOT NULL,
email VARCHAR(100) NOT NULL,
password VARCHAR(100) NOT NULL,
PRIMARY KEY(user_id)
);

CREATE TABLE restaurant (
res_id INT NOT NULL,
res_name VARCHAR(100) NOT NULL,
image VARCHAR(100) NOT NULL,
desc_res VARCHAR(100) NOT NULL,
PRIMARY KEY(res_id)
);

CREATE TABLE rate_res (
user_id INT NOT NULL,
res_id INT NOT NULL,
amount INT NOT NULL,
date_rate DATETIME NOT NULL,
PRIMARY KEY(user_id, res_id),
CONSTRAINT FK_rate_res_user_id FOREIGN KEY(user_id) REFERENCES user_infor(user_id),
CONSTRAINT FK_rate_res_res_id FOREIGN KEY(res_id) REFERENCES restaurant(res_id)
);

CREATE TABLE like_res (
user_id INT NOT NULL,
res_id INT NOT NULL,
date_like DATETIME NOT NULL,
PRIMARY KEY(user_id, res_id),
CONSTRAINT FK_like_res_user_id FOREIGN KEY(user_id) REFERENCES user_infor(user_id),
CONSTRAINT FK_like_res_res_id FOREIGN KEY(res_id) REFERENCES restaurant(res_id)
);

CREATE TABLE food (
food_id INT NOT NULL,
food_name VARCHAR(100) NOT NULL,
image VARCHAR(100) NOT NULL,
price FLOAT NOT NULL,
desc_food VARCHAR(100) NOT NULL,
type_id INT NOT NULL,
PRIMARY KEY(food_id),
CONSTRAINT FK_food_type_id FOREIGN KEY(type_id) REFERENCES food_type(type_id)
);

CREATE TABLE order_infor (
user_id INT NOT NULL,
food_id INT NOT NULL,
amount INT NOT NULL,
code VARCHAR(100) NOT NULL,
arr_sub_id VARCHAR(100) NOT NULL,
PRIMARY KEY(user_id, food_id),
CONSTRAINT FK_order_infor_user_id FOREIGN KEY(user_id) REFERENCES user_infor(user_id),
CONSTRAINT FK_order_infor_food_id FOREIGN KEY(food_id) REFERENCES food(food_id)
);

CREATE TABLE sub_food (
sub_id INT NOT NULL,
sub_name VARCHAR(100) NOT NULL,
sub_price FLOAT NOT NULL,
food_id INT NOT NULL,
PRIMARY KEY(sub_id),
CONSTRAINT FK_sub_food_food_id FOREIGN KEY(food_id) REFERENCES food(food_id)
);

CREATE TABLE food_type (
type_id INT NOT NULL,
type_name VARCHAR(100),
PRIMARY KEY(type_id)
);

--1.Tìm 5 người đã like nhà hàng nhiều nhất. hiển thị thông tin user_id, full_name

SELECT
user_id,
full_name,
like_times
FROM (
SELECT
user_infor.user_id,
user_infor.full_name,
COUNT() AS like_times,
DENSE_RANK() OVER (ORDER BY COUNT() DESC) AS rank
FROM
user_infor
JOIN like_res ON user_infor.user_id = like_res.user_id
GROUP BY user_infor.user_id, user_infor.full_name
) AS ranked
WHERE rank <= 5;

--2.Tìm 2 nhà hàng có lượt like nhiều nhất.

SELECT
res_id,
res_name,
like_times
FROM (
SELECT
restaurant.res_id,
restaurant.res_name,
COUNT () AS like_times,
DENSE_RANK() OVER (ORDER BY COUNT() DESC) AS rank
FROM like_res
JOIN restaurant ON like_res.res_id = restaurant.res_id
GROUP BY restaurant.res_id, restaurant.res_name
) AS ranked
WHERE rank <= 2;

--3.Tìm người đã đặt hàng nhiều nhất.

SELECT
user_id,
full_name,
order_times
FROM (
SELECT
user_infor.user_id,
user_infor.full_name,
COUNT() AS order_times,
DENSE_RANK() OVER (ORDER BY COUNT() DESC) AS rank
FROM
user_infor
JOIN order_infor ON user_infor.user_id = order_infor.user_id
GROUP BY user_infor.user_id, user_infor.full_name
) AS ranked
WHERE rank = 1;

--4.Tìm người dùng không hoạt động trong hệ thống (không đặt hàng, không like, không đánh giá nhà hàng).

SELECT
ui.user_id,
ui.full_name,
ui.email,
ui.password
FROM
user_infor ui
LEFT JOIN like_res lr ON ui.user_id = lr.user_id
LEFT JOIN rate_res rr ON ui.user_id = rr.user_id
LEFT JOIN order_infor oi ON ui.user_id = oi.user_id
WHERE
lr.user_id ISNULL
AND rr.user_id IS NULL
AND oi.user_id IS NULL;

--5.Tính trung bình sub_food của một food.

SELECT
AVG(food_count) AS average_subfood_of_food
FROM (
SELECT
COUNT(*) AS food_count
FROM
sub_food sf
LEFT JOIN food f ON f.food_id = sf.food_id
GROUP BY
f.food_id,
f.food_name
) AS subquery_alias;