
### Schema
![[Pasted image 20230206171935.png]]

- Setup the database
```mysql
CREATE SCHEMA `sns` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
- Setup the user table
```mysql
CREATE TABLE `sns`.`user` (  
  `id` BIGINT NOT NULL AUTO_INCREMENT,  
  `firstName` VARCHAR(50) NULL DEFAULT NULL,  
  `middleName` VARCHAR(50) NULL DEFAULT NULL,  
  `lastName` VARCHAR(50) NULL DEFAULT NULL,  
  `username` VARCHAR(50) NULL DEFAULT NULL,  
  `mobile` VARCHAR(15) NULL,  
  `email` VARCHAR(50) NULL,  
  `passwordHash` VARCHAR(32) NOT NULL,  
  `registeredAt` DATETIME NOT NULL,  
  `lastLogin` DATETIME NULL DEFAULT NULL,  
  `intro` TINYTEXT NULL DEFAULT NULL,  
  `profile` TEXT NULL DEFAULT NULL,  
  PRIMARY KEY (`id`),  
  UNIQUE INDEX `uq_username` (`username` ASC),  
  UNIQUE INDEX `uq_mobile` (`mobile` ASC),  
  UNIQUE INDEX `uq_email` (`email` ASC) );
```

- Add the user_friends table
```mysql
CREATE TABLE `sns`.`user_friend` (  
  `id` BIGINT NOT NULL AUTO_INCREMENT,  
  `sourceId` BIGINT NOT NULL,  
  `targetId` BIGINT NOT NULL,  
  `type` SMALLINT NOT NULL DEFAULT 0,  
  `status` SMALLINT NOT NULL DEFAULT 0,  
  `createdAt` DATETIME NOT NULL,  
  `updatedAt` DATETIME NULL DEFAULT NULL,  
  `notes` TEXT NULL DEFAULT NULL,  
  PRIMARY KEY (`id`),  
  INDEX `idx_friend_source` (`sourceId` ASC),  
  CONSTRAINT `fk_friend_source`  
    FOREIGN KEY (`sourceId`)  
    REFERENCES `sns`.`user` (`id`)  
    ON DELETE NO ACTION  
    ON UPDATE NO ACTION);  
  
ALTER TABLE `sns`.`user_friend`   
ADD INDEX `idx_friend_target` (`targetId` ASC);  
ALTER TABLE `sns`.`user_friend`   
ADD CONSTRAINT `fk_friend_target`  
  FOREIGN KEY (`targetId`)  
  REFERENCES `sns`.`user` (`id`)  
  ON DELETE NO ACTION  
  ON UPDATE NO ACTION;  
  
ALTER TABLE `sns`.`user_friend` ADD UNIQUE `uq_friend`(`sourceId`, `targetId`);
```

- Setup the user posts table 
```mysql
CREATE TABLE `sns`.`user_post` (  
  `id` BIGINT NOT NULL AUTO_INCREMENT,  
  `userId` BIGINT NOT NULL,  
  `senderId` BIGINT NOT NULL,  
  `message` TINYTEXT NULL DEFAULT NULL,  
  `createdAt` DATETIME NOT NULL,  
  `updatedAt` DATETIME NULL DEFAULT NULL,  
  PRIMARY KEY (`id`),  
  INDEX `idx_upost_user` (`userId` ASC),  
  CONSTRAINT `fk_upost_user`  
    FOREIGN KEY (`userId`)  
    REFERENCES `sns`.`user` (`id`)  
    ON DELETE NO ACTION  
    ON UPDATE NO ACTION);  
  
ALTER TABLE `sns`.`user_post`   
ADD INDEX `idx_upost_sender` (`senderId` ASC);  
ALTER TABLE `sns`.`user_post`   
ADD CONSTRAINT `fk_upost_sender`  
  FOREIGN KEY (`senderId`)  
  REFERENCES `sns`.`user` (`id`)  
  ON DELETE NO ACTION  
  ON UPDATE NO ACTION;
```