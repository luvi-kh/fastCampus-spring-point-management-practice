# point-management-practice
포인트 관리하기 예시 SpringBatch 프로젝트

*fastCampus-spring-point-management-practice
docker rm mysql point-mysql
sudo systemctl stop mysql
sudo lsof -i :33060
sudo kill -9 1200
docker run -p 33060:3306 --name point-mysql -e MYSQL_ROOT_PASSWORD=password -d mysql:latest

mkdir -p ~/mysql_data
docker run -p 33060:3306 \
--name point-mysql \
-e MYSQL_ROOT_PASSWORD=password \
-v ~/mysql_data:/var/lib/mysql \
-d mysql:latest


docker exec -i -t b925e471302a /bin/bash
mysql -uroot -p

INSERT INTO `point_wallet`
(user_id, amount)
VALUES ('user1', 0);
INSERT INTO `point_wallet`
(user_id, amount)
VALUES ('user2', 0);

DELIMITER //
CREATE PROCEDURE bulkInsert()
BEGIN
DECLARE i INT DEFAULT 1;
DECLARE random_amount bigint DEFAULT 1;
WHILE (i <= 10000) DO
SET random_amount = FLOOR(1000 + (RAND() * 9000));
INSERT INTO `point_reservation`
(point_wallet_id, amount, earned_date, available_days, is_executed)
VALUES (1, random_amount, '2021-09-13', 10, 0);
SET i = i+1;
END WHILE;
END;
//
DELIMITER ;

call bulkInsert();

drop procedure if exists bulkInsert;

DELIMITER //
CREATE PROCEDURE bulkNoTarget()
BEGIN
DECLARE i INT DEFAULT 1;
DECLARE random_amount bigint DEFAULT 1;
WHILE (i <= 100000) DO
SET random_amount = FLOOR(1000 + (RAND() * 9000));
INSERT INTO `point_reservation`
(point_wallet_id, amount, earned_date, available_days, is_executed)
VALUES (1, random_amount, ‘2030-12-30’, 10, 0);
SET i = i+1;
END WHILE;
END;
//
DELIMITER ;

java -jar build/libs/fastCampus-spring-point-management-practice-1.0-SNAPSHOT.jar --job.name=expirePointJob today=2021-09-05
java -jar build/libs/fastCampus-spring-point-management-practice-1.0-SNAPSHOT.jar --job.name=messageExpiredPointJob today=2021-09-04
java -jar build/libs/fastCampus-spring-point-management-practice-1.0-SNAPSHOT.jar --job.name=messageExpireSoonPointJob today=2021-09-02
java -jar build/libs/fastCampus-spring-point-management-practice-1.0-SNAPSHOT.jar --job.name=executePointReservationJob today=2021-09-02

INSERT INTO point_reservation  (id, amount, available_days, earned_date, is_executed, point_wallet_id)
VALUES (1, 1000, 10, '2021-09-02', 0, 1);

alter table point_reservation add index idx_executed_earndate(is_executed, earned_date);




