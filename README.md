# INFO-605
Project Description
Many small clinics, especially specialty practices such as cardiology offices, still rely heavily on paper charting to maintain patient records. An issue with paper charts is that information can get lost or can become difficult to obtain, especially in emergency scenarios where accessing a patient’s history and medications is critical. Although there are electronic health record (EHR) systems such as Epic and Cerner, these systems are expensive and too technically complex for smaller clinics. Our goal is to design and implement an affordable, secure, and scalable database-driven system tailored for small cardiology clinics. The system will centralize key patient data, streamline workflows, support continuity of care, and ultimately enhance patient satisfaction and provider efficiency.

In-scope Details
 In this system we will integrate information regarding patient’s personal and demographic data, insurance and billing, diagnoses, medications, referrals, visit history, results of cardiac testing (eg. EKG’s, echocardiograms, ultrasounds, stress tests, cardiac scans), recent hospitalization information, and referrals to outside specialists. 

Out-scope Details
This system will not contain comprehensive information about past hospitalizations and will retain information from the last five years, full integration with external hospital systems, and complex billing and claims process systems.  

Requirements Specifications
The data objects in this system will include the patient, doctor, visit information, diagnosis, and medication or treatment. In addition, insurance and referral information will be collected as well. These data objects would provide a full overview of a patient’s medical record at the office so that their care team can more easily manage the patient’s treatment plan. The technology also needs to be HIPAA compliant, so any protected health information will be de-identified if it needs to be reviewed by external entities. Furthermore, only staff trained in HIPAA compliance will have access to the database.
 
Data Dictionary
1.	Patient 
a.	PatientID (PK)
b.	Name
c.	DOB
d.	Gender
e.	Phone
f.	Email
g.	Address
h.	InsuranceProvider
2.	Doctor 
a.	DoctorID (PK)
b.	Name
c.	Specialty
d.	Phone
e.	Email
3.	Visit 
a.	VisitID (PK)
b.	VisitDate
c.	Reason
d.	Notes
e.	PatientID (FK)
f.	DoctorID (FK)
4.	Diagnosis 
a.	DiagnosisID (PK)
b.	Name
c.	Description
d.	ICD_Code
5.	PatientDiagnosis 
a.	PatientID (FK)
b.	DiagnosisID (FK)
c.	DateDiagnosed
d.	DiagnosedByDoctorID (FK)
e.	(Composite PK: PatientID + DiagnosisID+DateDiagnosed)
6.	Medication 
a.	MedicationID (PK)
b.	Name
c.	Dosage
d.	Frequency
7.	Prescription 
a.	PrescriptionID (PK)
b.	PatientID (FK)
c.	MedicationID (FK)
d.	PrescribedBy (FK → DoctorID)
e.	StartDate
f.	EndDate
8.	Test 
a.	TestID (PK)
b.	TestType (e.g., EKG, Echo)
c.	ResultSummary
d.	TestDate
e.	OrderedByDoctorID (FK)
f.	PatientID (FK)
9.	Referral 
a.	ReferralID (PK)
b.	PatientID (FK)
c.	DoctorID (FK)
d.	ReferredTo (Provider/Clinic Name)
e.	Reason
f.	ReferralDate
g.	Status (Pending, Completed, etc.)
10.	Billing 
a.	BillingID (PK)
b.	PatientID (FK)
c.	Amount
d.	Billing Date
e.	PaymentStatus (Paid/Unpaid)
f.	InsuranceClaimed (Yes/No)
 
Relationships
1.	Patient - Visit
a.	A Patient can have multiple Visits, but each Visit is associated with only one Patient.
b.	Relationship: One-to-Many (1:M)
c.	PatientID (FK) in Visit refers to Patient.
2.	Patient - Prescription
a.	A Patient can receive multiple Prescriptions, but each Prescription is associated with only one Patient.
b.	Relationship: One-to-Many (1:M)
c.	PatientID (FK) in Prescription refers to Patient.
3.	Doctor - Visit
a.	A Doctor can conduct multiple Visits, but each Visit is associated with only one Doctor.
b.	Relationship: One-to-Many (1:M)
c.	DoctorID (FK) in Visit refers to Doctor.
4.	Patient - Diagnosis
a.	A Patient can have multiple Diagnoses, and a Diagnosis can be associated with multiple Patients (e.g., common conditions).
b.	Relationship: Many-to-Many (M:N)
c.	This is resolved through the PatientDiagnosis table, where each entry links a Patient to a Diagnosis.
d.	PatientID (FK) in PatientDiagnosis refers to Patient and DiagnosisID (FK) refers to Diagnosis.
5.	Doctor - Diagnosis
a.	A Doctor can diagnose multiple Patients with various Diagnoses.
b.	Relationship: One-to-Many (1:M)
c.	DoctorID (FK) in PatientDiagnosis refers to Doctor.
6.	Doctor - Test
a.	A Doctor can order multiple Tests for a Patient.
b.	Relationship: One-to-Many (1:M)
c.	DoctorID (FK) in Test refers to Doctor.
7.	Patient - Test
a.	A Patient can undergo multiple Tests, but each Test is associated with only one Patient.
b.	Relationship: One-to-Many (1:M)
c.	PatientID (FK) in Test refers to Patient.
8.	Doctor - Referral
a.	A Doctor can make multiple Referrals for a Patient.
b.	Relationship: One-to-Many (1:M)
c.	DoctorID (FK) in Referral refers to Doctor.
9.	Patient - Referral
a.	A Patient can be referred by multiple Doctors to other specialists, but each Referral is associated with only one Patient.
b.	Relationship: One-to-Many (1:M)
c.	PatientID (FK) in Referral refers to Patient.
10.	Patient - Billing
a.	A Patient can have multiple Billing records (for different visits, treatments, or tests).
b.	Relationship: One-to-Many (1:M)
c.	PatientID (FK) in Billing refers to Patient.
 
Assumptions
1.	Data Retention:
a.	The system will store patient data only for the past 5 years. Older records may be archived or deleted according to the clinic’s data retention policy.
2.	Doctor and Specialist Relationship 
a. Doctors can refer patients to specialists (e.g., cardiologists, surgeons) within or outside the clinic. However, only one doctor per referral is tracked. 
b. The Doctor entity includes a Boolean attribute isSpecialist to distinguish between general doctors and specialists. 
c. In the Referral table: 
-	referringDoctorID refers to the doctor who creates the referral and can be any doctor (no restriction on isSpecialist). 
-	specialistDoctorID must refer to a doctor where isSpecialist = TRUE. This ensures that referrals are made only to doctors who are marked as specialists. 
The system is responsible for validating that specialistDoctorID refers only to doctors with isSpecialist = TRUE. 

3.	Diagnosis and PatientDiagnosis Relationship 
a. The Diagnosis entity stores standardized diagnostic information, including ICD (International Classification of Diseases) codes and descriptions, which are reused across multiple patients. 
b. The PatientDiagnosis entity captures the patient-specific diagnosis event, including the date of diagnosis and the doctor who made it. This structure supports normalization and enables tracking repeated diagnoses over time for the same patient by different doctors or across multiple visits. 
4.	Insurance Information:
a.	Patients can have only one InsuranceProvider recorded at a time, but their Insurance details are kept private and are not shared with external systems except for billing purposes.
5.	Medication Handling:
a.	A Prescription includes a Medication for a specific Patient, and the system does not track the medication status after the prescription ends. It is assumed the medication is only valid for the prescribed duration (StartDate to EndDate).
6.	Test Results:
a.	Test results are stored along with TestType and a ResultSummary, but only the doctor who ordered the test can view the complete results (as indicated by OrderedByDoctorID).
7.	Referral Status:
a.	Referrals are tracked with statuses like "Pending" or "Completed." It’s assumed the status is updated only when a specialist completes the referral.
8.	Billing:
a.	Each Billing record corresponds to a single Patient. The billing system does not support complex claims or multi-layered insurance coverage. It's assumed the system supports basic billing without dealing with external claims processing.
b.	ICD_10 is a standard coding system developed by the WHO that is used to classify diseases and health problems. 
9.	Single Clinic System:
a.	This database design assumes that the clinic is a single, self-contained entity, without integration to larger hospital or clinic management systems (e.g., Epic or Cerner). The system will not handle complex integration with external electronic health systems.
 
 
Relational Schema
Patient(patientID, firstName, lastName, gender, phone, email, DOB, address, insuranceProvider)
Doctor (doctorID, firstName, lastName, gender, phone, email, specialty, isSpecialist)  
Diagnosis (diagnosisID, name, description, ICD_Code)
PatientDiagnosis(patientID, diagnosisID, dateDiagnosed, diagnosedByDoctorID)
•	Composite Primary Key: (patientID, diagnosisID, dateDiagnosed)
•	Foreign key patientID references Patient(patientID)
•	Foreign key diagnosisID references Diagnosis(diagnosisID)
•	Foreign key diagnosedByDoctorID references Doctor(doctorID)
Visit (visitID, visitDate, reason, notes, patientID, doctorID)
•	Foreign key patientID references Patient(patientID)
•	Foreign key doctorID references Doctor(doctorID)
Prescription (prescriptionID, startDate, endDate, patientID, doctorID)
•	Foreign key patientID references Patient(patientID)
•	Foreign key doctorID references Doctor(doctorID)

Medication (medicationID, name, dosage, frequency)
PrescriptionMedication(prescriptionID, medicationID)
•	Composite Primary Key: (prescriptionID, medicationID)
•	Foreign key prescriptionID references Prescription(prescriptionID)
•	Foreign key medicationID references Medication(medicationID)

Test(testID, testType, resultSummary, testDate, patientID, doctorID)
•	Foreign key patientID references Patient(patientID)
•	Foreign key doctorID references Doctor(doctorID)
Referral(referralID, referredTo, reason, referralDate, status, patientID, referringDoctorID, specialistDoctorID )  
•	Foreign key patientID references Patient(patientID)  
•	Foreign key  referringDoctorID REFERENCES Doctor(doctorID), 
•	Foreign key  specialistDoctorID REFERENCES Doctor(doctorID) 

Billing(billingID, amount, billingDate, paymentStatus, insuranceClaimed, patientID)
•	Foreign key patientID references Patient(patientID)


Database Implementation
Patient Table
Contains information about patients in the cardiology clinic
Column	Description	Data Type	Domain	Nullable	PK	FK
PATIENTID	Patient MRN	CHAR(12)	ALL	NO	Yes	No
PATIENTNAME	Patient name	VARCHAR(25)	ALL	NO	No	No
DATEOFBIRTH	Patient Date of Birth	DATE	ALL	YES	No	No
PATIENTGENDER	Patient Gender	VARCHAR(25)	ALL	NO	No	No
PATIENTPHONE	Patient phone number	CHAR(10)	ALL	YES	No	No
PATIENTEMAIL	Patient email	VARCHAR(25)	ALL	NO	No	No
PATIENTADDRESS	Patient address	VARCHAR(25)	ALL	NO	No	No
INSURANCEPROVIDER	Patient insurance provider	VARCHAR(25)	ALL	NO	No	No
 
CONSTRAINT	TYPE	ON DELETE
PATIENT_PK	Primary Key	-
PATIENT_NN_NAME	NOT NULL	-
PATIENT_NN_GENDER	NOT NULL	-
PATIENT_NN_EMAIL	NOT NULL	-
PATIENT_NN_ADDRESS	NOT NULL	-
PATIENT_NN_INSURANCE	NOT NULL	-
PATIENT_UQ_ID	Unique	-
 
Doctor Table
Contains information regarding the doctors working in the clinic
Column	Description	Data Type	Domain	Nullable	PK	FK
DOCTORID	Doctor ID	CHAR(12)	ALL	NO	Yes	No
DOCTORNAME	Doctor name	VARCHAR(25)	ALL	NO	No	No
SPECIALITY	Doctor’s specialty	VARCHAR(25)	ALL	NO	No	No
DOCTORPHONE	Doctor phone number	CHAR(10)	ALL	YES	No	No
DOCTOREMAIL	Doctor email address	VARCHAR(25)	ALL	NO	No	No
 
 
CONSTRAINT	TYPE	ON DELETE
DOCTOR_PK	Primary Key	-
DOCTOR_NN_NAME	NOT NULL	-
DOCTOR_NN_SPECIALITY	NOT NULL	-
DOCTOR_NN_EMAIL	NOT NULL	-

Medication Table
Contains information about medications prescribed to patients
Column	Description	Data Type	Domain	Nullable	PK	FK	Constraint Type	On Delete
MedicationID	Unique medication identifier	CHAR(10)	All	No	✔		PRIMARY KEY	-
Name	Name of the medication	VARCHAR2(50)	All	No			NOT NULL	-
Dosage	Dosage of medication	VARCHAR2(20)	All	No			NOT NULL	-
Frequency	How often it should be taken	VARCHAR2(30)	All	No			NOT NULL	-


Prescription Table
Contains information about prescriptions issued to patients
Column	Description	Data Type	Domain	Nullable	PK	FK	Constraint Type	On Delete
PrescriptionID	Unique prescription identifier	CHAR(10)	All	No	✔		PRIMARY KEY	-
PatientID	Patient receiving the prescription	CHAR(12)	All	No		✔	FOREIGN KEY	-
MedicationID	Prescribed medication	CHAR(10)	All	No		✔	FOREIGN KEY	-
PrescribedBy	Doctor who prescribed the medication	CHAR(12)	All	No		✔	FOREIGN KEY	-
StartDate	Date the prescription starts	DATE	All	No			NOT NULL	-
EndDate	Date the prescription ends	DATE	All	Yes				-

Foreign Keys:
•	PatientID → Patient(PatientID)
•	MedicationID → Medication(MedicationID)
•	PrescribedBy → Doctor(DoctorID)

Test Table
Contains information about medical tests ordered for patients
Column	Description	Data Type	Domain	Nullable	PK	FK	Constraint Type	On Delete
TestID	Unique test identifier	CHAR(10)	All	No	✔		PRIMARY KEY	-
TestType	Type of test (e.g., EKG, Echo)	VARCHAR2(20)	All	No			NOT NULL	-
ResultSummary	Summary of test results	VARCHAR2(100)	All	Yes				-
TestDate	Date the test was conducted	DATE	All	No			NOT NULL	-
OrderedByDoctorID	Doctor who ordered the test	CHAR(12)	All	No		✔	FOREIGN KEY	-
PatientID	Patient for whom the test was ordered	CHAR(12)	All	No		✔	FOREIGN KEY	-
Foreign Keys:
•	OrderedByDoctorID → Doctor(DoctorID)
•	PatientID → Patient(PatientID)


Referral
Contains information about referrals made for patients     
Column Name	Description	Data Type	Domain	Nullable	PK	FK
ReferralID	Unique identifier for the referral	CHAR(10)	All	No	Yes	No
ReferredTo	Department or specialty the patient is referred to	VARCHAR2(100)	All	No	No	No
Reason	Reason for the referral	VARCHAR2(255)	All	No	No	No
ReferralDate	Date the referral was made	DATE	Valid dates	No	No	No
ReferralStatus	Current status of the referral (e.g., Open, Closed)	VARCHAR2(20)	e.g., 'Open', 'Pending'	No	No	No
PatientID	ID of the patient being referred	CHAR(12)	Matches Patient ID	No	No	Yes
ReferringDoctorID	ID of the doctor who initiated the referral	CHAR(12)	Matches Doctor ID	No	No	Yes
SpecialistDoctorID	ID of the specialist receiving the referral	CHAR(12)	Matches Doctor ID	Yes	No	Yes


Constraint	Type	Delete
Referral_PK	Primary Key	-
Referral_FK_Patient	Foreign Key	-
Referral_FK_Referring	Foreign Key	-
Referral_FK_Specialist	Foreign Key	-


Billing
Contains information about how patients are billed for visits and testing.
Column	Description	DataType	Domain	Nullable	PK	FK
BILLINGID	Billing identifier	CHAR(10)	All	No	Yes	No
AMOUNT	Amount billed (e.g., $1234.56)	NUMBER(10,2)	All	No	No	No
BILLINGDATE	Date the bill was issued	DATE	All	No	No	No
PAYMENTSTATUS	Payment status (e.g. “Paid”, “Due”, “Overdue”)	VARCHAR2(20)	All	No	No	No
INSURANCECLAIMED	“Yes” or “No” to indicate if insurance was claimed	VARCHAR2(3)	(Yes/No)	No	No	No
PATIENTID	Patient associated with this billing	CHAR(12)	All	No	No	Yes

Constraint	Type	On Delete
BILLING_NN_ID	NOT NULL (BILLINGID)	–
BILLING_NN_AMOUNT	NOT NULL (AMOUNT)	–
BILLING_NN_BILLINGDATE	NOT NULL (BILLINGDATE)	–
BILLING_NN_PAYMENTSTATUS	NOT NULL (PAYMENTSTATUS)	–
BILLING_NN_INSURANCECLAIMED	NOT NULL (INSURANCECLAIMED)	–
BILLING_NN_PATIENTID	NOT NULL (PATIENTID)	–
BILLING_PK	PRIMARY KEY (BILLINGID)	–
BILLING_FK_PATIENT	FOREIGN KEY (PATIENTID) REFERENCES Patient(patientID)	–

Visit 
Contains information about patient visits
Column	Description	Data Type	Domain	Nullable	PK	FK
VISITID	Visit ID	CHAR(12)	ALL	NO	YES	NO
VISITDATE	Date of visit	Date	ALL	NO	NO	NO
REASON	Reason for visit	VARCHAR2(500)	ALL	NO	NO	NO
NOTES	Physician’s notes from the visit	VARCHAR2(500)	ALL	NO	NO	NO
PATIENTID	Patient MRN	CHAR(12)	ALL	NO	NO	YES
DOCTORID	Doctor ID	CHAR(12)	ALL	NO	NO	YES

Constraint	Type	On Delete
VisitID_pk	PRIMARY KEY	-
VisitDate_nn	NOT NULL	-
Reason_nn	NOT NULL	-
Notes_nn	NOT NULL	-
Patient_visit_fk	FOREIGN KEY	-
Doctor_visit_fk	FOREIGN KEY	-

Diagnosis
Contains information about patients diagnoses
Column	Description	Data Type	Domain	Nullable	PK	FK
DIAGNOSISID	Diagnosis ID	CHAR(12)	ALL	NO	YES	NO
DIAGNOSISNAME	Name of the patient’s diagnosis	VARCHAR2(50)	ALL	NO	NO	NO
DIAGNOSIS
DESCRIPTION	Description of the diagnosis	VARCHAR2(500)	ALL	NO	NO	NO
ICDCODE	ICD Code	CHAR(20)	ALL	NO	NO	NO

Constraint	Type	On Delete
DiagnosisID_pk	PRIMARY KEY	-
DiagnosisName_nn	NOT NULL	-
Diagnosisdesc_nn	NOT NULL	-
Icdcode_nn	NOT NULL	-


Patient Diagnosis
Contains information about patient diagnoses
Column	Description	Data Type	Domain	Nullable	PK	FK
DATEDIAGNOSED	Date of Diagnosis	Date	ALL	NO	YES	NO
PATIENTID	Patient MRN	CHAR(12)	ALL	NO	NO	YES
DIAGNOSISID	ID for Diagnosis	CHAR(12)	ALL	NO	NO	YES
DOCTORID	Doctor ID 	CHAR(12)	ALL	NO	NO	YES

Constraint	Type	On Delete
PatientDiagnosis_pk	PRIMARY KEY	- 
PatientID_fk	FOREIGN KEY	-
DiagnosisID_fk	FOREIGN KEY	-
DoctorID_fk	FOREIGN KEY	-

 

 
Summary
In this database, we wanted to create a system that organizes all the information collected in a cardiology clinic. It brings everything, doctor, patient, and insurance data, into one place, making things easier for everyone involved. One of the main benefits is that doctors can keep track of any referrals they make to outside specialists, which helps them follow up with patients. It also allows doctors at other facilities to see what tests have already been done, so they’re not starting from scratch when trying to understand why a patient was referred.
One thing that could make this system even better is adding a patient portal, where patients can log in and see information from their visits and any tests they’ve had done. A lot of larger hospitals already have this feature, and it really helps improve patient experience and satisfaction after their appointments.
Overall, this database is a great starting point for smaller clinics. It can always be adjusted and expanded based on the clinic’s needs over time.
