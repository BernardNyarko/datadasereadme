# datadasereadme
## Student: Bernard Nyarko   
- Lecturer: Dr Mary Kim     
- Course: Advanced Database Management    
- Project Title: Hospital Management System 
-- phpMyAdmin SQL Dump
-- version 5.0.2
-- https://www.phpmyadmin.net/
--
-- Host: 127.0.0.1
-- Generation Time: Nov 17, 2020 at 10:27 PM
-- Server version: 10.4.14-MariaDB
-- PHP Version: 7.4.9

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

--
-- Database: `hospital_management_system`
--

DELIMITER $$
--
-- Procedures
--
CREATE DEFINER=`root`@`localhost` PROCEDURE `AdmittedPatients_Cursor` ()  NO SQL
Begin
DECLARE Patient_ID varchar(10);
DECLARE Patient_FNAME varchar(25);
DECLARE Patient_LNAME varchar(25);
DECLARE Doctors_ID varchar(10);
DECLARE Doctor_LN varchar(25);
DECLARE Diagnosis varchar(100);
DECLARE Prescription varchar(100);
DECLARE Admission_Room varchar(10);
DECLARE Admission_Date date;
DECLARE done integer DEFAULT 0;
DECLARE AdmittedPatients_Cursor cursor for SELECT patients.Patient_ID, patients.First_Name, patients.Last_Name, doctors.Doctor_ID, doctors.Last_Name, medical_history.Diagnosis, medical_history.Prescriptions, admission.Room_No, admission.Date FROM admission LEFT JOIN medical_history ON admission.Date=medical_history.Date LEFT JOIN patients ON patients.Patient_ID=admission.Patient_ID LEFT JOIN doctors ON doctors.Doctor_ID=admission.Doctor_ID WHERE doctors.Doctor_ID IN(SELECT admission.Doctor_ID FROM admission);
declare continue handler for not found set done=1;
OPEN AdmittedPatients_Cursor;
staffClient: LOOP
 FETCH AdmittedPatients_Cursor INTO Patient_ID, Patient_FNAME, Patient_LNAME, Doctors_ID, Doctor_LN, Diagnosis, Prescription, Admission_Room, Admission_Date;
 If done = 1 then
 Leave staffClient;
 end if;
 SELECT Patient_ID, Patient_FNAME, Patient_LNAME, Doctors_ID, Doctor_LN, Diagnosis, Prescription, Admission_Room, Admission_Date;
 
END LOOP staffClient;
Close AdmittedPatients_Cursor;
END$$

DELIMITER ;

-- --------------------------------------------------------

--
-- Table structure for table `admission`
--

CREATE TABLE `admission` (
  `Room_No` varchar(10) NOT NULL,
  `Branch_ID` varchar(10) NOT NULL,
  `Patient_ID` varchar(10) NOT NULL,
  `Doctor_ID` varchar(10) NOT NULL,
  `Date` date NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `admission`
--

INSERT INTO `admission` (`Room_No`, `Branch_ID`, `Patient_ID`, `Doctor_ID`, `Date`) VALUES
('01', 'HB1', '1HB1', 'MD1', '2017-01-05'),
('02', 'HB3', '1HB3', 'MD2', '2020-02-10'),
('03', 'HB5', '1HB5', 'MD6', '2020-11-10');

--
-- Triggers `admission`
--
DELIMITER $$
CREATE TRIGGER `Admission_Trigger` BEFORE INSERT ON `admission` FOR EACH ROW BEGIN
 DECLARE msg varchar(100);
 IF new.Patient_ID = ANY(SELECT admission.Patient_ID FROM admission WHERE new.Doctor_ID=admission.Doctor_ID AND new.Branch_ID=admission.Branch_ID AND new.Date =admission.Date)
 THEN
 set msg ='This Patient is Already Admitted';
 SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = msg;
 END IF;
END
$$
DELIMITER ;

-- --------------------------------------------------------

--
-- Table structure for table `billing`
--

CREATE TABLE `billing` (
  `Bill_ID` varchar(10) NOT NULL,
  `Patient_ID` varchar(10) NOT NULL,
  `Doctor_ID` varchar(10) NOT NULL,
  `Date` date NOT NULL,
  `Bill` varchar(10) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `billing`
--

INSERT INTO `billing` (`Bill_ID`, `Patient_ID`, `Doctor_ID`, `Date`, `Bill`) VALUES
('B01', '1HB1', 'MD1', '2017-01-05', '$10,000.00'),
('B03', '1HB5', 'MD6', '2020-02-10', '$500.00'),
('BO2', '1HB3', 'MD2', '2020-11-10', '$35,000.00');

-- --------------------------------------------------------

--
-- Table structure for table `doctors`
--

CREATE TABLE `doctors` (
  `Doctor_ID` varchar(10) NOT NULL,
  `First_Name` varchar(25) NOT NULL,
  `Last_Name` varchar(25) NOT NULL,
  `Age` int(11) NOT NULL,
  `Gender` varchar(25) NOT NULL,
  `Address` varchar(50) NOT NULL,
  `Telephone_Number` varchar(25) NOT NULL,
  `Speciality` varchar(50) NOT NULL,
  `Branch_ID` varchar(10) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `doctors`
--

INSERT INTO `doctors` (`Doctor_ID`, `First_Name`, `Last_Name`, `Age`, `Gender`, `Address`, `Telephone_Number`, `Speciality`, `Branch_ID`) VALUES
('MD1', 'Bernard', 'Nyarko', 32, 'Male', '95 Best Western Street', '+1 281-754-1562', 'Cardiology', 'HB1'),
('MD2', 'Mary', 'Kim', 47, 'Female', '7 College Station', '+1 936-261-9859', 'Dentistry', 'HB2'),
('MD3', 'Umair', 'Malik', 25, 'Male', '34 Prairie View Avenue', '+1 583-024-1111', 'Gynaecology', 'HB3'),
('MD4', 'Muhammad', 'Ghazi', 22, 'Male', '10 Magdalene Avenue', '+1 999-588-2275', 'Dermatology', 'HB4'),
('MD6', 'Jamil', 'Ahmed', 23, 'Male', '12 Washington Avenue', '+7 592-746-1155', 'General Medical Officer', 'HB5');

-- --------------------------------------------------------

--
-- Stand-in structure for view `doctor_bernard_nyarko`
-- (See below for the actual view)
--
CREATE TABLE `doctor_bernard_nyarko` (
`Doctor_ID` varchar(10)
,`Speciality` varchar(50)
,`Patient_ID` varchar(10)
,`First_Name` varchar(25)
,`Last_Name` varchar(25)
,`Branch_ID` varchar(10)
,`Diagnosis` varchar(100)
,`Prescriptions` varchar(100)
,`Date` date
);

-- --------------------------------------------------------

--
-- Stand-in structure for view `doctor_jamil_ahmed`
-- (See below for the actual view)
--
CREATE TABLE `doctor_jamil_ahmed` (
`Doctor_ID` varchar(10)
,`Speciality` varchar(50)
,`Patient_ID` varchar(10)
,`First_Name` varchar(25)
,`Last_Name` varchar(25)
,`Branch_ID` varchar(10)
,`Diagnosis` varchar(100)
,`Prescriptions` varchar(100)
,`Date` date
);

-- --------------------------------------------------------

--
-- Stand-in structure for view `doctor_mary_kim`
-- (See below for the actual view)
--
CREATE TABLE `doctor_mary_kim` (
`Doctor_ID` varchar(10)
,`Speciality` varchar(50)
,`Patient_ID` varchar(10)
,`First_Name` varchar(25)
,`Last_Name` varchar(25)
,`Branch_ID` varchar(10)
,`Diagnosis` varchar(100)
,`Prescriptions` varchar(100)
,`Date` date
);

-- --------------------------------------------------------

--
-- Stand-in structure for view `doctor_muhammad_ghazi`
-- (See below for the actual view)
--
CREATE TABLE `doctor_muhammad_ghazi` (
`Doctor_ID` varchar(10)
,`Speciality` varchar(50)
,`Patient_ID` varchar(10)
,`First_Name` varchar(25)
,`Last_Name` varchar(25)
,`Branch_ID` varchar(10)
,`Diagnosis` varchar(100)
,`Prescriptions` varchar(100)
,`Date` date
);

-- --------------------------------------------------------

--
-- Stand-in structure for view `doctor_umair_malik`
-- (See below for the actual view)
--
CREATE TABLE `doctor_umair_malik` (
`Doctor_ID` varchar(10)
,`Speciality` varchar(50)
,`Patient_ID` varchar(10)
,`First_Name` varchar(25)
,`Last_Name` varchar(25)
,`Branch_ID` varchar(10)
,`Diagnosis` varchar(100)
,`Prescriptions` varchar(100)
,`Date` date
);

-- --------------------------------------------------------

--
-- Table structure for table `hospital_branch`
--

CREATE TABLE `hospital_branch` (
  `Branch_ID` varchar(10) NOT NULL,
  `City` varchar(30) NOT NULL,
  `Address` varchar(100) NOT NULL,
  `Post_Code` varchar(8) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `hospital_branch`
--

INSERT INTO `hospital_branch` (`Branch_ID`, `City`, `Address`, `Post_Code`) VALUES
('HB1', 'STAFFORD', '11929 W AIRPORT BLVD', '77477'),
('HB2', 'HOUSTON', '22572 E AIRPORT BLVD', '77577'),
('HB3', 'PRAIRIE VIEW', '2003 PHILLIPS STREET', '77445'),
('HB4', 'SAN ANTONIO', '77589 MEADOWS PLACE', '77478'),
('HB5', 'FORT WORT', '12345 GOLD STREET', '77479');

-- --------------------------------------------------------

--
-- Table structure for table `medical_history`
--

CREATE TABLE `medical_history` (
  `Patient_ID` varchar(10) NOT NULL,
  `Doctor_ID` varchar(10) NOT NULL,
  `Branch_ID` varchar(10) NOT NULL,
  `Diagnosis` varchar(100) DEFAULT NULL,
  `Prescriptions` varchar(100) DEFAULT NULL,
  `Date` date NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `medical_history`
--

INSERT INTO `medical_history` (`Patient_ID`, `Doctor_ID`, `Branch_ID`, `Diagnosis`, `Prescriptions`, `Date`) VALUES
('1HB1', 'MD1', 'HB1', 'Atherosclerosis', 'Atorvastatin', '2017-01-05'),
('1HB1', 'MD1', 'HB1', 'Atrial Fibrillation', 'Procainamide', '2018-10-02'),
('1HB1', 'MD6', 'HB1', 'Rickettsioses', 'Doxycycline', '2019-10-27'),
('1HB2', 'MD1', 'HB2', 'Hypertension', 'Hydrochlorothiazide', '2017-04-13'),
('1HB2', 'MD2', 'HB2', 'Gum Disease', 'Spiramycin', '2020-07-12'),
('1HB3', 'MD1', 'HB3', 'Congestive Heart', 'Furosemide', '2018-04-22'),
('1HB3', 'MD2', 'HB3', 'Tooth Decay', 'Fluoride', '2020-02-10'),
('1HB3', 'MD3', 'HB3', 'Gonorrhea', 'Zithromycin', '2020-11-05'),
('1HB4', 'MD4', 'HB4', 'Psoriasis', 'Corticosteroid', '2020-09-17'),
('1HB4', 'MD6', 'HB4', 'Migraine', 'Rizatriptan', '2019-07-19'),
('1HB4', 'MD6', 'HB4', 'Common Cold', 'Ibuprofen', '2020-11-17'),
('1HB5', 'MD3', 'HB5', 'Pelvic Inflammation', 'Metronidazole', '2017-12-05'),
('1HB5', 'MD6', 'HB5', 'Pregnant', 'Diphenhydramine', '2019-06-02'),
('2HB1', 'MD1', 'HB1', 'Coronary Heart', 'Nitroglycerin', '2017-09-21'),
('2HB2', 'MD3', 'HB2', 'Syphilis', 'Penicillin', '2020-11-01'),
('2HB2', 'MD4', 'HB2', 'Skin Cancer', '5-FU', '2020-06-03'),
('2HB2', 'MD6', 'HB2', 'Diarrhea', 'Azithromycin', '2020-02-24'),
('2HB3', 'MD2', 'HB3', 'Oral Cancer', 'Radiation Therapy', '2020-03-19'),
('2HB3', 'MD4', 'HB3', 'Eczema', 'Crisaborole', '2020-07-09'),
('2HB3', 'MD6', 'HB3', 'Stomach Ache', 'Acetaminophen', '2020-04-03'),
('2HB4', 'MD2', 'HB4', 'Bad Breath', 'Hydrogen peroxide', '2020-01-16'),
('2HB4', 'MD3', 'HB4', 'Uterine Fibroids', 'Gonadotropin-Releasing Hormone', '2020-07-17'),
('2HB4', 'MD4', 'HB4', 'Acne', 'Tetracycline', '2020-08-18'),
('2HB4', 'MD6', 'HB4', 'Conjunctivitis', 'Moxifloxacin', '2020-08-14'),
('2HB5', 'MD2', 'HB5', 'Mouth Sores', 'Chlorhexidine', '2020-04-30'),
('2HB5', 'MD4', 'HB5', 'nail infections', 'Hydrogen Peroxide', '2020-11-04'),
('2HB5', 'MD6', 'HB5', 'Cholera ', 'Tetracycline', '2020-11-10');

-- --------------------------------------------------------

--
-- Table structure for table `patients`
--

CREATE TABLE `patients` (
  `Patient_ID` varchar(10) NOT NULL,
  `First_Name` varchar(25) NOT NULL,
  `Last_Name` varchar(25) NOT NULL,
  `Age` int(4) NOT NULL,
  `Gender` varchar(25) NOT NULL,
  `Address` varchar(50) NOT NULL,
  `Telephone_No` varchar(15) NOT NULL,
  `DOB` date NOT NULL,
  `Branch_ID` varchar(10) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `patients`
--

INSERT INTO `patients` (`Patient_ID`, `First_Name`, `Last_Name`, `Age`, `Gender`, `Address`, `Telephone_No`, `DOB`, `Branch_ID`) VALUES
('1HB1', 'Brian', 'Adams', 70, 'Male', '12 Beechnut Street', '+1 281-754-0000', '1950-07-05', 'HB1'),
('1HB2', 'Micheal', 'Simpson', 55, 'Male', '459 California Blvd', '+1 909-251-6790', '1970-01-23', 'HB2'),
('1HB3', 'Phillip ', 'Collins', 35, 'Male', '278 Las Vegas', '+1 467-924-7193', '1985-04-29', 'HB3'),
('1HB4', 'Collin', 'Harris', 45, 'Female', '9 Prairie View BLVD', '+1 583-024-7592', '1975-08-17', 'HB4'),
('1HB5', 'Sarah', 'Abraham', 99, 'Female', '777 Bethlehem Street', '+1 775-588-2275', '1921-05-07', 'HB5'),
('2HB1', 'Jessica ', 'Cole', 21, 'Female', '8 Oregan View', '+1 347-298-3461', '1999-01-05', 'HB1'),
('2HB2', 'Victor', 'Davidson', 10, '\'Male', '45 Downtown Houston', '+1 884-602-7451', '2010-08-21', 'HB2'),
('2HB3', 'Frank', 'Mcginty', 27, 'Male', '11 Fantastic Street', '+1 476-208-7462', '1993-11-14', 'HB3'),
('2HB4', 'Lucy', 'Quist', 39, 'Female', '55 Magdalene Avenue', '+1 937-824-7501', '1981-05-18', 'HB4'),
('2HB5', 'Donald', 'Biden', 75, 'Male', '869 Washington Avenue', '+7 592-746-5013', '1945-08-12', 'HB5');

-- --------------------------------------------------------

--
-- Table structure for table `rooms`
--

CREATE TABLE `rooms` (
  `Room_No` varchar(10) NOT NULL,
  `Price` varchar(10) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `rooms`
--

INSERT INTO `rooms` (`Room_No`, `Price`) VALUES
('01', '$20.00'),
('02', '$30.00'),
('03', '$40.00');

-- --------------------------------------------------------

--
-- Structure for view `doctor_bernard_nyarko`
--
DROP TABLE IF EXISTS `doctor_bernard_nyarko`;

CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `doctor_bernard_nyarko`  AS  select `doctors`.`Doctor_ID` AS `Doctor_ID`,`doctors`.`Speciality` AS `Speciality`,`medical_history`.`Patient_ID` AS `Patient_ID`,`patients`.`First_Name` AS `First_Name`,`patients`.`Last_Name` AS `Last_Name`,`medical_history`.`Branch_ID` AS `Branch_ID`,`medical_history`.`Diagnosis` AS `Diagnosis`,`medical_history`.`Prescriptions` AS `Prescriptions`,`medical_history`.`Date` AS `Date` from ((`medical_history` left join `patients` on(`patients`.`Patient_ID` = `medical_history`.`Patient_ID`)) left join `doctors` on(`medical_history`.`Doctor_ID` = `doctors`.`Doctor_ID`)) where `doctors`.`Doctor_ID` = 'MD1' ;

-- --------------------------------------------------------

--
-- Structure for view `doctor_jamil_ahmed`
--
DROP TABLE IF EXISTS `doctor_jamil_ahmed`;

CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `doctor_jamil_ahmed`  AS  select `doctors`.`Doctor_ID` AS `Doctor_ID`,`doctors`.`Speciality` AS `Speciality`,`medical_history`.`Patient_ID` AS `Patient_ID`,`patients`.`First_Name` AS `First_Name`,`patients`.`Last_Name` AS `Last_Name`,`medical_history`.`Branch_ID` AS `Branch_ID`,`medical_history`.`Diagnosis` AS `Diagnosis`,`medical_history`.`Prescriptions` AS `Prescriptions`,`medical_history`.`Date` AS `Date` from ((`medical_history` left join `patients` on(`patients`.`Patient_ID` = `medical_history`.`Patient_ID`)) left join `doctors` on(`medical_history`.`Doctor_ID` = `doctors`.`Doctor_ID`)) where `doctors`.`Doctor_ID` = 'MD6' ;

-- --------------------------------------------------------

--
-- Structure for view `doctor_mary_kim`
--
DROP TABLE IF EXISTS `doctor_mary_kim`;

CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `doctor_mary_kim`  AS  select `doctors`.`Doctor_ID` AS `Doctor_ID`,`doctors`.`Speciality` AS `Speciality`,`medical_history`.`Patient_ID` AS `Patient_ID`,`patients`.`First_Name` AS `First_Name`,`patients`.`Last_Name` AS `Last_Name`,`medical_history`.`Branch_ID` AS `Branch_ID`,`medical_history`.`Diagnosis` AS `Diagnosis`,`medical_history`.`Prescriptions` AS `Prescriptions`,`medical_history`.`Date` AS `Date` from ((`medical_history` left join `patients` on(`patients`.`Patient_ID` = `medical_history`.`Patient_ID`)) left join `doctors` on(`medical_history`.`Doctor_ID` = `doctors`.`Doctor_ID`)) where `doctors`.`Doctor_ID` = 'MD2' ;

-- --------------------------------------------------------

--
-- Structure for view `doctor_muhammad_ghazi`
--
DROP TABLE IF EXISTS `doctor_muhammad_ghazi`;

CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `doctor_muhammad_ghazi`  AS  select `doctors`.`Doctor_ID` AS `Doctor_ID`,`doctors`.`Speciality` AS `Speciality`,`medical_history`.`Patient_ID` AS `Patient_ID`,`patients`.`First_Name` AS `First_Name`,`patients`.`Last_Name` AS `Last_Name`,`medical_history`.`Branch_ID` AS `Branch_ID`,`medical_history`.`Diagnosis` AS `Diagnosis`,`medical_history`.`Prescriptions` AS `Prescriptions`,`medical_history`.`Date` AS `Date` from ((`medical_history` left join `patients` on(`patients`.`Patient_ID` = `medical_history`.`Patient_ID`)) left join `doctors` on(`medical_history`.`Doctor_ID` = `doctors`.`Doctor_ID`)) where `doctors`.`Doctor_ID` = 'MD4' ;

-- --------------------------------------------------------

--
-- Structure for view `doctor_umair_malik`
--
DROP TABLE IF EXISTS `doctor_umair_malik`;

CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `doctor_umair_malik`  AS  select `doctors`.`Doctor_ID` AS `Doctor_ID`,`doctors`.`Speciality` AS `Speciality`,`medical_history`.`Patient_ID` AS `Patient_ID`,`patients`.`First_Name` AS `First_Name`,`patients`.`Last_Name` AS `Last_Name`,`medical_history`.`Branch_ID` AS `Branch_ID`,`medical_history`.`Diagnosis` AS `Diagnosis`,`medical_history`.`Prescriptions` AS `Prescriptions`,`medical_history`.`Date` AS `Date` from ((`medical_history` left join `patients` on(`patients`.`Patient_ID` = `medical_history`.`Patient_ID`)) left join `doctors` on(`medical_history`.`Doctor_ID` = `doctors`.`Doctor_ID`)) where `doctors`.`Doctor_ID` = 'MD3' ;

--
-- Indexes for dumped tables
--

--
-- Indexes for table `admission`
--
ALTER TABLE `admission`
  ADD PRIMARY KEY (`Room_No`,`Patient_ID`,`Doctor_ID`,`Date`),
  ADD KEY `Branch_ID` (`Branch_ID`),
  ADD KEY `Doctor_ID` (`Doctor_ID`),
  ADD KEY `Patient_ID` (`Patient_ID`);

--
-- Indexes for table `billing`
--
ALTER TABLE `billing`
  ADD PRIMARY KEY (`Bill_ID`) USING BTREE,
  ADD KEY `billing_ibfk_1` (`Patient_ID`) USING BTREE,
  ADD KEY `Doctor_ID` (`Doctor_ID`);

--
-- Indexes for table `doctors`
--
ALTER TABLE `doctors`
  ADD PRIMARY KEY (`Doctor_ID`),
  ADD KEY `Branch_ID` (`Branch_ID`);

--
-- Indexes for table `hospital_branch`
--
ALTER TABLE `hospital_branch`
  ADD PRIMARY KEY (`Branch_ID`);

--
-- Indexes for table `medical_history`
--
ALTER TABLE `medical_history`
  ADD PRIMARY KEY (`Patient_ID`,`Doctor_ID`,`Date`),
  ADD KEY `Branch_ID` (`Branch_ID`),
  ADD KEY `Doctor_ID` (`Doctor_ID`);

--
-- Indexes for table `patients`
--
ALTER TABLE `patients`
  ADD PRIMARY KEY (`Patient_ID`),
  ADD KEY `Branch_ID` (`Branch_ID`);

--
-- Indexes for table `rooms`
--
ALTER TABLE `rooms`
  ADD PRIMARY KEY (`Room_No`) USING BTREE;

--
-- Constraints for dumped tables
--

--
-- Constraints for table `admission`
--
ALTER TABLE `admission`
  ADD CONSTRAINT `admission_ibfk_1` FOREIGN KEY (`Branch_ID`) REFERENCES `hospital_branch` (`Branch_ID`),
  ADD CONSTRAINT `admission_ibfk_2` FOREIGN KEY (`Doctor_ID`) REFERENCES `doctors` (`Doctor_ID`),
  ADD CONSTRAINT `admission_ibfk_3` FOREIGN KEY (`Patient_ID`) REFERENCES `patients` (`Patient_ID`),
  ADD CONSTRAINT `admission_ibfk_4` FOREIGN KEY (`Room_No`) REFERENCES `rooms` (`Room_No`);

--
-- Constraints for table `billing`
--
ALTER TABLE `billing`
  ADD CONSTRAINT `billing_ibfk_1` FOREIGN KEY (`Patient_ID`) REFERENCES `patients` (`Patient_ID`),
  ADD CONSTRAINT `billing_ibfk_2` FOREIGN KEY (`Doctor_ID`) REFERENCES `doctors` (`Doctor_ID`);

--
-- Constraints for table `doctors`
--
ALTER TABLE `doctors`
  ADD CONSTRAINT `doctors_ibfk_1` FOREIGN KEY (`Branch_ID`) REFERENCES `hospital_branch` (`Branch_ID`);

--
-- Constraints for table `medical_history`
--
ALTER TABLE `medical_history`
  ADD CONSTRAINT `medical_history_ibfk_1` FOREIGN KEY (`Branch_ID`) REFERENCES `hospital_branch` (`Branch_ID`),
  ADD CONSTRAINT `medical_history_ibfk_2` FOREIGN KEY (`Doctor_ID`) REFERENCES `doctors` (`Doctor_ID`),
  ADD CONSTRAINT `medical_history_ibfk_3` FOREIGN KEY (`Patient_ID`) REFERENCES `patients` (`Patient_ID`);

--
-- Constraints for table `patients`
--
ALTER TABLE `patients`
  ADD CONSTRAINT `patients_ibfk_1` FOREIGN KEY (`Branch_ID`) REFERENCES `hospital_branch` (`Branch_ID`);
COMMIT;

/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
