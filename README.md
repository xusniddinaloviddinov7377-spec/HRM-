# HRM-
Maxsus platforma
-- ============================================================
--  OTM KADRLAR BOSHQARUV TIZIMI — PostgreSQL Ma'lumotlar Bazasi
--  Versiya: 1.0
--  Sana: 2026-05-25
--  Tavsif: Oliy ta'lim muassasasi uchun professional HRM tizimi
-- ============================================================

-- Kengaytmalar
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";


-- ============================================================
--  1. ILMIY DARAJA (Scientific Degree)
-- ============================================================
CREATE TABLE scientific_degrees (
    id          SERIAL PRIMARY KEY,
    name_uz     VARCHAR(100) NOT NULL,
    name_ru     VARCHAR(100),
    code        VARCHAR(20) UNIQUE,
    sort_order  SMALLINT DEFAULT 0,
    created_at  TIMESTAMP DEFAULT NOW()
);

INSERT INTO scientific_degrees (name_uz, name_ru, code, sort_order) VALUES
    ('Yo''q',          'Нет',                'NONE',    0),
    ('Magistr',        'Магистр',            'MSc',     1),
    ('Fan nomzodi',    'Кандидат наук',      'CSc',     2),
    ('PhD',            'PhD',                'PhD',     3),
    ('DSc',            'Доктор наук',        'DSc',     4);


-- ============================================================
--  2. ILMIY UNVON (Scientific Title)
-- ============================================================
CREATE TABLE scientific_titles (
    id          SERIAL PRIMARY KEY,
    name_uz     VARCHAR(100) NOT NULL,
    name_ru     VARCHAR(100),
    code        VARCHAR(20) UNIQUE,
    sort_order  SMALLINT DEFAULT 0,
    created_at  TIMESTAMP DEFAULT NOW()
);

INSERT INTO scientific_titles (name_uz, name_ru, code, sort_order) VALUES
    ('Yo''q',                  'Нет',                       'NONE',   0),
    ('Katta ilmiy xodim',      'Старший научный сотрудник', 'SNS',    1),
    ('Dotsent',                'Доцент',                    'DOC',    2),
    ('Professor',              'Профессор',                 'PROF',   3);


-- ============================================================
--  3. LAVOZIMLAR (Positions)
-- ============================================================
CREATE TABLE positions (
    id          SERIAL PRIMARY KEY,
    name_uz     VARCHAR(150) NOT NULL,
    name_ru     VARCHAR(150),
    code        VARCHAR(30) UNIQUE,
    category    VARCHAR(50) CHECK (category IN (
                    'rahbariyat', 'professor_oqituvchi',
                    'yordamchi', 'ma_muriy', 'texnik'
                )),
    sort_order  SMALLINT DEFAULT 0,
    is_active   BOOLEAN DEFAULT TRUE,
    created_at  TIMESTAMP DEFAULT NOW()
);

INSERT INTO positions (name_uz, code, category, sort_order) VALUES
    ('Rektor',              'RECTOR',     'rahbariyat',         1),
    ('Prorektor',           'PRORECTOR',  'rahbariyat',         2),
    ('Dekan',               'DEAN',       'rahbariyat',         3),
    ('Kafedra mudiri',      'HEAD_DEPT',  'professor_oqituvchi',4),
    ('Professor',           'PROFESSOR',  'professor_oqituvchi',5),
    ('Dotsent',             'DOCENT',     'professor_oqituvchi',6),
    ('Katta o''qituvchi',   'SR_TEACHER', 'professor_oqituvchi',7),
    ('O''qituvchi',         'TEACHER',    'professor_oqituvchi',8),
    ('Assistent',           'ASSISTANT',  'professor_oqituvchi',9),
    ('Laborant',            'LABORANT',   'yordamchi',          10),
    ('Metodist',            'METHODIST',  'ma_muriy',           11),
    ('Mutaxassis',          'SPECIALIST', 'ma_muriy',           12),
    ('Bosh mutaxassis',     'CHIEF_SPEC', 'ma_muriy',           13),
    ('Bo''lim boshlig''i',  'DEPT_HEAD',  'ma_muriy',           14);


-- ============================================================
--  4. XODIM TURLARI (Employee Types)
-- ============================================================
CREATE TABLE employee_types (
    id          SERIAL PRIMARY KEY,
    name_uz     VARCHAR(100) NOT NULL,
    name_ru     VARCHAR(100),
    code        VARCHAR(30) UNIQUE NOT NULL,
    description TEXT,
    created_at  TIMESTAMP DEFAULT NOW()
);

INSERT INTO employee_types (name_uz, code, description) VALUES
    ('Asosiy',           'MAIN',     'Asosiy ish joyi'),
    ('Ichki o''rindosh', 'INT_PART', 'Ichki o''rindoshlik (bir muassasa ichida)'),
    ('Tashqi o''rindosh','EXT_PART', 'Tashqi o''rindoshlik (boshqa muassasadan)'),
    ('Soatbay',          'HOURLY',   'Soatbay to''lov asosida ishlaydigan');


-- ============================================================
--  5. FAKULTETLAR (Faculties)
-- ============================================================
CREATE TABLE faculties (
    id          SERIAL PRIMARY KEY,
    name_uz     VARCHAR(200) NOT NULL,
    name_ru     VARCHAR(200),
    code        VARCHAR(20) UNIQUE,
    dean_name   VARCHAR(200),
    phone       VARCHAR(20),
    email       VARCHAR(100),
    is_active   BOOLEAN DEFAULT TRUE,
    created_at  TIMESTAMP DEFAULT NOW(),
    updated_at  TIMESTAMP DEFAULT NOW()
);

INSERT INTO faculties (name_uz, code) VALUES
    ('Iqtisodiyot',        'ECON'),
    ('Filologiya',         'PHIL'),
    ('Axborot texnologiyalari', 'IT'),
    ('Pedagogika',         'PED'),
    ('Huquq',              'LAW'),
    ('Tarix',              'HIST'),
    ('Matematika',         'MATH'),
    ('Tabiiy fanlar',      'NAT');


-- ============================================================
--  6. KAFEDRALAR (Departments / Chairs)
-- ============================================================
CREATE TABLE departments (
    id          SERIAL PRIMARY KEY,
    name_uz     VARCHAR(200) NOT NULL,
    name_ru     VARCHAR(200),
    code        VARCHAR(30) UNIQUE,
    faculty_id  INT NOT NULL REFERENCES faculties(id) ON DELETE RESTRICT,
    head_name   VARCHAR(200),
    phone       VARCHAR(20),
    email       VARCHAR(100),
    room_number VARCHAR(20),
    is_active   BOOLEAN DEFAULT TRUE,
    created_at  TIMESTAMP DEFAULT NOW(),
    updated_at  TIMESTAMP DEFAULT NOW()
);

INSERT INTO departments (name_uz, code, faculty_id) VALUES
    ('Dasturiy injiniring',         'SE',        3),
    ('Axborot tizimlari',           'IS',        3),
    ('Kompyuter muhandisligi',      'CE',        3),
    ('Moliya va kredit',            'FIN',       1),
    ('Iqtisodiyot nazariyasi',      'ECON_TH',   1),
    ('O''zbek tili va adabiyoti',   'UZB_LIT',   2),
    ('Xorijiy tillar',              'FOREIGN',   2),
    ('Pedagogika va psixologiya',   'PED_PSY',   4),
    ('Fuqarolik huquqi',            'CIV_LAW',   5),
    ('Oliy matematika',             'HIGH_MATH', 7);


-- ============================================================
--  7. XODIMLAR ASOSIY MA'LUMOTLARI (Employees)
-- ============================================================
CREATE TABLE employees (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    employee_code   VARCHAR(20) UNIQUE NOT NULL,       -- EMP-0001
    -- Shaxsiy ma'lumotlar
    last_name       VARCHAR(100) NOT NULL,
    first_name      VARCHAR(100) NOT NULL,
    middle_name     VARCHAR(100),
    birth_date      DATE NOT NULL,
    gender          CHAR(1) CHECK (gender IN ('M', 'F')) NOT NULL,
    nationality     VARCHAR(50) DEFAULT 'O''zbek',
    -- Hujjat raqamlari
    passport_series VARCHAR(20) UNIQUE,
    jshshir         CHAR(14) UNIQUE,                   -- 14 raqamli
    stir            VARCHAR(20) UNIQUE,
    -- Aloqa
    phone           VARCHAR(20),
    phone_extra     VARCHAR(20),
    email           VARCHAR(100) UNIQUE,
    telegram_id     VARCHAR(100),
    -- Manzil
    address         TEXT,
    region          VARCHAR(100),
    -- Qo'shimcha
    photo_url       TEXT,
    qr_code         TEXT,
    -- Harbiy va pensiya
    military_status VARCHAR(50) CHECK (military_status IN (
                        'harbiy_xizmat_otgan', 'zahirada',
                        'qonuniy_ozod', 'mansub_emas'
                    )),
    pension_status  BOOLEAN DEFAULT FALSE,
    -- Holat
    status          VARCHAR(20) DEFAULT 'active'
                        CHECK (status IN ('active', 'inactive', 'fired')),
    -- Tizim
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW(),
    created_by      VARCHAR(100)
);

-- Avto employee_code uchun sequence
CREATE SEQUENCE employee_code_seq START 1;
CREATE OR REPLACE FUNCTION generate_employee_code()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.employee_code IS NULL OR NEW.employee_code = '' THEN
        NEW.employee_code := 'EMP-' || LPAD(nextval('employee_code_seq')::TEXT, 4, '0');
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_employee_code
    BEFORE INSERT ON employees
    FOR EACH ROW EXECUTE FUNCTION generate_employee_code();


-- ============================================================
--  8. ISH MA'LUMOTLARI (Employment)
--     Xodimning tashkiliy joylashuvi
-- ============================================================
CREATE TABLE employment (
    id                  SERIAL PRIMARY KEY,
    employee_id         UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    -- Tashkiliy joylashuv
    employee_type_id    INT NOT NULL REFERENCES employee_types(id),
    faculty_id          INT REFERENCES faculties(id),
    department_id       INT REFERENCES departments(id),
    position_id         INT NOT NULL REFERENCES positions(id),
    -- Moliyaviy shartlar
    staff_rate          NUMERIC(4,2) DEFAULT 1.0
                            CHECK (staff_rate IN (0.25, 0.5, 0.75, 1.0, 1.25, 1.5)),
    workload_hours      NUMERIC(5,1),                  -- haftalik soat
    -- Sanalar
    work_start_date     DATE NOT NULL,
    work_end_date       DATE,
    -- Shartnoma
    contract_number     VARCHAR(50),
    contract_start      DATE,
    contract_end        DATE,
    -- Buyruq
    order_number        VARCHAR(50),
    order_date          DATE,
    -- Holat
    employment_status   VARCHAR(30) DEFAULT 'active'
                            CHECK (employment_status IN (
                                'active', 'vacation', 'sick_leave',
                                'academic_leave', 'fired', 'retired'
                            )),
    is_primary          BOOLEAN DEFAULT TRUE,          -- asosiy ish joyi
    notes               TEXT,
    -- Tizim
    created_at          TIMESTAMP DEFAULT NOW(),
    updated_at          TIMESTAMP DEFAULT NOW(),
    created_by          VARCHAR(100),
    -- Bir xodim bir muassasada bir vaqtda faqat bitta asosiy ish joyi
    CONSTRAINT unique_primary_employment
        EXCLUDE USING gist (
            employee_id WITH =,
            is_primary WITH =
        ) WHERE (is_primary = TRUE AND employment_status = 'active')
);


-- ============================================================
--  9. ILMIY MA'LUMOTLAR (Academic Info)
-- ============================================================
CREATE TABLE academic_info (
    id                      SERIAL PRIMARY KEY,
    employee_id             UUID UNIQUE NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    -- Ilmiy daraja va unvon
    scientific_degree_id    INT REFERENCES scientific_degrees(id),
    scientific_title_id     INT REFERENCES scientific_titles(id),
    degree_date             DATE,                      -- diplom sanasi
    degree_institution      VARCHAR(300),              -- qayerda olgan
    dissertation_topic      TEXT,                      -- dissertatsiya mavzusi
    scientific_supervisor   VARCHAR(200),              -- ilmiy rahbar
    -- Oliy ma'lumot
    higher_edu_institution  VARCHAR(300),
    specialty               VARCHAR(200),
    graduation_year         SMALLINT,
    -- Malaka oshirish
    last_training_date      DATE,
    next_training_date      DATE,
    training_institution    VARCHAR(300),
    -- Ilmiy ishlar statistikasi
    publications_count      SMALLINT DEFAULT 0,
    foreign_publications    SMALLINT DEFAULT 0,
    books_count             SMALLINT DEFAULT 0,
    patents_count           SMALLINT DEFAULT 0,
    -- Til bilishi
    languages               TEXT[],                    -- {'uzbek','russian','english'}
    -- Tizim
    created_at              TIMESTAMP DEFAULT NOW(),
    updated_at              TIMESTAMP DEFAULT NOW()
);


-- ============================================================
--  10. MEHNAT SHARTNOMALAR (Contracts)
-- ============================================================
CREATE TABLE contracts (
    id                  SERIAL PRIMARY KEY,
    employee_id         UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    employment_id       INT REFERENCES employment(id),
    contract_number     VARCHAR(100) NOT NULL,
    contract_type       VARCHAR(30) CHECK (contract_type IN (
                            'muddatli', 'muddatsiz', 'sinovdan_otish'
                        )),
    start_date          DATE NOT NULL,
    end_date            DATE,
    signed_date         DATE,
    salary              NUMERIC(12,2),
    currency            CHAR(3) DEFAULT 'UZS',
    status              VARCHAR(20) DEFAULT 'active'
                            CHECK (status IN ('active', 'expired', 'terminated', 'draft')),
    file_url            TEXT,                          -- skanerlangan fayl
    notes               TEXT,
    created_at          TIMESTAMP DEFAULT NOW(),
    updated_at          TIMESTAMP DEFAULT NOW(),
    created_by          VARCHAR(100)
);


-- ============================================================
--  11. BUYRUQLAR (Orders)
-- ============================================================
CREATE TABLE orders (
    id              SERIAL PRIMARY KEY,
    order_number    VARCHAR(50) NOT NULL,
    order_date      DATE NOT NULL,
    order_type      VARCHAR(50) NOT NULL CHECK (order_type IN (
                        'ishga_qabul', 'boshatish', 'yillik_tatil',
                        'bemorligi', 'tug_ruq_tatil', 'lavozim_ozgarishi',
                        'stavka_ozgarishi', 'bonus', 'hayfsan',
                        'akademik_tatil', 'shartnoma_uzaytirish', 'boshqa'
                    )),
    employee_id     UUID NOT NULL REFERENCES employees(id) ON DELETE RESTRICT,
    description     TEXT,
    effective_date  DATE,
    signed_by       VARCHAR(200),
    status          VARCHAR(20) DEFAULT 'draft'
                        CHECK (status IN ('draft', 'signed', 'cancelled')),
    file_url        TEXT,
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW(),
    created_by      VARCHAR(100),
    UNIQUE(order_number, order_date)
);


-- ============================================================
--  12. TA'TIL HISOBI (Vacation Balance)
-- ============================================================
CREATE TABLE vacation_balances (
    id              SERIAL PRIMARY KEY,
    employee_id     UUID UNIQUE NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    year            SMALLINT NOT NULL DEFAULT EXTRACT(YEAR FROM NOW()),
    total_days      SMALLINT DEFAULT 56,               -- OTMda 56 kun
    used_days       SMALLINT DEFAULT 0,
    remaining_days  SMALLINT GENERATED ALWAYS AS (total_days - used_days) STORED,
    updated_at      TIMESTAMP DEFAULT NOW(),
    UNIQUE(employee_id, year)
);


-- ============================================================
--  13. TA'TIL ARIZALARI (Vacations)
-- ============================================================
CREATE TABLE vacations (
    id              SERIAL PRIMARY KEY,
    employee_id     UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    vacation_type   VARCHAR(30) NOT NULL CHECK (vacation_type IN (
                        'yillik', 'bemorligi', 'tug_ruq',
                        'akademik', 'haqsiz', 'ijtimoiy', 'boshqa'
                    )),
    start_date      DATE NOT NULL,
    end_date        DATE NOT NULL,
    days_count      SMALLINT GENERATED ALWAYS AS
                        (end_date - start_date + 1) STORED,
    order_id        INT REFERENCES orders(id),
    status          VARCHAR(20) DEFAULT 'pending'
                        CHECK (status IN (
                            'pending', 'approved', 'rejected', 'cancelled', 'completed'
                        )),
    approved_by     VARCHAR(200),
    approved_at     TIMESTAMP,
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW()
);


-- ============================================================
--  14. DAVOMAT (Attendance)
-- ============================================================
CREATE TABLE attendance (
    id              BIGSERIAL PRIMARY KEY,
    employee_id     UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    work_date       DATE NOT NULL,
    check_in        TIME,
    check_out       TIME,
    status          VARCHAR(20) DEFAULT 'present'
                        CHECK (status IN (
                            'present', 'absent', 'vacation',
                            'sick', 'late', 'remote', 'holiday'
                        )),
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    UNIQUE(employee_id, work_date)
);


-- ============================================================
--  15. SHTATLAR JADVALI (Staff Schedule)
-- ============================================================
CREATE TABLE staff_schedule (
    id              SERIAL PRIMARY KEY,
    faculty_id      INT NOT NULL REFERENCES faculties(id),
    department_id   INT REFERENCES departments(id),
    position_id     INT NOT NULL REFERENCES positions(id),
    employee_type   VARCHAR(20) DEFAULT 'MAIN'
                        CHECK (employee_type IN ('MAIN','INT_PART','EXT_PART','HOURLY')),
    approved_count  SMALLINT NOT NULL DEFAULT 1,       -- tasdiqlangan shtat
    filled_count    SMALLINT DEFAULT 0,                -- band
    academic_year   VARCHAR(10) NOT NULL,              -- '2025-2026'
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW(),
    CONSTRAINT check_filled CHECK (filled_count <= approved_count)
);


-- ============================================================
--  16. VAKANSIYALAR (Vacancies)
-- ============================================================
CREATE TABLE vacancies (
    id              SERIAL PRIMARY KEY,
    faculty_id      INT REFERENCES faculties(id),
    department_id   INT REFERENCES departments(id),
    position_id     INT NOT NULL REFERENCES positions(id),
    required_degree INT REFERENCES scientific_degrees(id),
    required_title  INT REFERENCES scientific_titles(id),
    staff_rate      NUMERIC(4,2) DEFAULT 1.0,
    announced_date  DATE DEFAULT CURRENT_DATE,
    deadline_date   DATE,
    requirements    TEXT,
    status          VARCHAR(20) DEFAULT 'open'
                        CHECK (status IN (
                            'open', 'in_review', 'closed', 'cancelled'
                        )),
    applicants_count SMALLINT DEFAULT 0,
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW(),
    updated_at      TIMESTAMP DEFAULT NOW(),
    created_by      VARCHAR(100)
);


-- ============================================================
--  17. HUJJATLAR (Employee Documents)
-- ============================================================
CREATE TABLE documents (
    id              SERIAL PRIMARY KEY,
    employee_id     UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    document_type   VARCHAR(50) NOT NULL CHECK (document_type IN (
                        'passport', 'diplom', 'mehnat_daftarchasi',
                        'buyruq', 'shartnoma', 'sertifikat',
                        'malaka_oshirish', 'harbiy_guvohnoma',
                        'ilmiy_daraja_diplomi', 'ilmiy_unvon_attestati',
                        'fotosurat', 'boshqa'
                    )),
    file_name       VARCHAR(300) NOT NULL,
    file_url        TEXT NOT NULL,
    file_size_kb    INT,
    mime_type       VARCHAR(100),
    upload_date     DATE DEFAULT CURRENT_DATE,
    expiry_date     DATE,                              -- amal qilish muddati
    description     TEXT,
    is_verified     BOOLEAN DEFAULT FALSE,
    verified_by     VARCHAR(200),
    verified_at     TIMESTAMP,
    created_at      TIMESTAMP DEFAULT NOW(),
    uploaded_by     VARCHAR(100)
);


-- ============================================================
--  18. KPI BAHOLASH (KPI Evaluations)
-- ============================================================
CREATE TABLE kpi_evaluations (
    id                  SERIAL PRIMARY KEY,
    employee_id         UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    period_year         SMALLINT NOT NULL,
    period_quarter      SMALLINT CHECK (period_quarter BETWEEN 1 AND 4),
    -- Baholash yo'nalishlari (har biri 25 balldan)
    teaching_quality    NUMERIC(5,2) DEFAULT 0 CHECK (teaching_quality BETWEEN 0 AND 25),
    scientific_activity NUMERIC(5,2) DEFAULT 0 CHECK (scientific_activity BETWEEN 0 AND 25),
    student_work        NUMERIC(5,2) DEFAULT 0 CHECK (student_work BETWEEN 0 AND 25),
    social_activity     NUMERIC(5,2) DEFAULT 0 CHECK (social_activity BETWEEN 0 AND 25),
    -- Jami (avto hisob)
    total_score         NUMERIC(5,2) GENERATED ALWAYS AS (
                            teaching_quality + scientific_activity +
                            student_work + social_activity
                        ) STORED,
    evaluated_by        VARCHAR(200),
    evaluation_date     DATE DEFAULT CURRENT_DATE,
    comments            TEXT,
    status              VARCHAR(20) DEFAULT 'draft'
                            CHECK (status IN ('draft', 'submitted', 'approved')),
    created_at          TIMESTAMP DEFAULT NOW(),
    updated_at          TIMESTAMP DEFAULT NOW(),
    UNIQUE(employee_id, period_year, period_quarter)
);


-- ============================================================
--  19. XABARNOMALAR (Notifications)
-- ============================================================
CREATE TABLE notifications (
    id              BIGSERIAL PRIMARY KEY,
    employee_id     UUID REFERENCES employees(id) ON DELETE CASCADE,
    type            VARCHAR(50) NOT NULL CHECK (type IN (
                        'contract_expiry', 'vacation_approved',
                        'vacation_rejected', 'document_expiry',
                        'kpi_due', 'ministry_report', 'system'
                    )),
    title           VARCHAR(300) NOT NULL,
    message         TEXT,
    channel         VARCHAR(20) DEFAULT 'system'
                        CHECK (channel IN ('system', 'telegram', 'email', 'sms')),
    is_read         BOOLEAN DEFAULT FALSE,
    sent_at         TIMESTAMP,
    created_at      TIMESTAMP DEFAULT NOW()
);


-- ============================================================
--  20. FAOLIYAT TARIXI (Audit Log)
-- ============================================================
CREATE TABLE audit_log (
    id              BIGSERIAL PRIMARY KEY,
    table_name      VARCHAR(100) NOT NULL,
    record_id       VARCHAR(100) NOT NULL,
    action          VARCHAR(10) CHECK (action IN ('INSERT','UPDATE','DELETE')),
    old_data        JSONB,
    new_data        JSONB,
    changed_by      VARCHAR(200),
    changed_at      TIMESTAMP DEFAULT NOW(),
    ip_address      INET
);


-- ============================================================
--  INDEKSLAR (Indexes) — Tezlashtirish uchun
-- ============================================================

-- Employees
CREATE INDEX idx_employees_status       ON employees(status);
CREATE INDEX idx_employees_jshshir      ON employees(jshshir);
CREATE INDEX idx_employees_passport     ON employees(passport_series);

-- Employment
CREATE INDEX idx_employment_employee    ON employment(employee_id);
CREATE INDEX idx_employment_faculty     ON employment(faculty_id);
CREATE INDEX idx_employment_dept        ON employment(department_id);
CREATE INDEX idx_employment_position    ON employment(position_id);
CREATE INDEX idx_employment_status      ON employment(employment_status);
CREATE INDEX idx_employment_contract    ON employment(contract_end);

-- Contracts
CREATE INDEX idx_contracts_employee     ON contracts(employee_id);
CREATE INDEX idx_contracts_end          ON contracts(end_date);
CREATE INDEX idx_contracts_status       ON contracts(status);

-- Orders
CREATE INDEX idx_orders_employee        ON orders(employee_id);
CREATE INDEX idx_orders_date            ON orders(order_date);
CREATE INDEX idx_orders_type            ON orders(order_type);

-- Vacations
CREATE INDEX idx_vacations_employee     ON vacations(employee_id);
CREATE INDEX idx_vacations_dates        ON vacations(start_date, end_date);
CREATE INDEX idx_vacations_status       ON vacations(status);

-- Attendance
CREATE INDEX idx_attendance_employee    ON attendance(employee_id);
CREATE INDEX idx_attendance_date        ON attendance(work_date);

-- Documents
CREATE INDEX idx_documents_employee     ON documents(employee_id);
CREATE INDEX idx_documents_type         ON documents(document_type);

-- KPI
CREATE INDEX idx_kpi_employee           ON kpi_evaluations(employee_id);
CREATE INDEX idx_kpi_period             ON kpi_evaluations(period_year, period_quarter);

-- Notifications
CREATE INDEX idx_notifications_employee ON notifications(employee_id);
CREATE INDEX idx_notifications_unread   ON notifications(employee_id) WHERE is_read = FALSE;

-- Audit
CREATE INDEX idx_audit_table            ON audit_log(table_name, record_id);
CREATE INDEX idx_audit_time             ON audit_log(changed_at);


-- ============================================================
--  FUNKSIYALAR VA TRIGGERLAR
-- ============================================================

-- updated_at avtomatik yangilash
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Barcha jadvallar uchun updated_at trigger
DO $$
DECLARE
    t TEXT;
BEGIN
    FOREACH t IN ARRAY ARRAY[
        'employees','employment','faculties','departments',
        'contracts','orders','vacations','staff_schedule',
        'vacancies','academic_info','kpi_evaluations'
    ] LOOP
        EXECUTE format(
            'CREATE TRIGGER trg_updated_at_%I
             BEFORE UPDATE ON %I
             FOR EACH ROW EXECUTE FUNCTION update_updated_at()',
            t, t
        );
    END LOOP;
END;
$$;

-- Shartnoma muddati tugayotganlarni aniqlash (30 kun qolganlar)
CREATE OR REPLACE FUNCTION get_expiring_contracts(days_ahead INT DEFAULT 30)
RETURNS TABLE (
    employee_name   TEXT,
    employee_code   VARCHAR,
    position_name   VARCHAR,
    department_name VARCHAR,
    contract_end    DATE,
    days_left       INT
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        e.last_name || ' ' || e.first_name       AS employee_name,
        e.employee_code,
        p.name_uz                                 AS position_name,
        d.name_uz                                 AS department_name,
        c.end_date,
        (c.end_date - CURRENT_DATE)::INT          AS days_left
    FROM contracts c
    JOIN employees e  ON e.id = c.employee_id
    JOIN employment em ON em.employee_id = e.id AND em.is_primary = TRUE
    JOIN positions p  ON p.id = em.position_id
    LEFT JOIN departments d ON d.id = em.department_id
    WHERE c.status = 'active'
      AND c.end_date IS NOT NULL
      AND c.end_date BETWEEN CURRENT_DATE AND CURRENT_DATE + days_ahead
    ORDER BY c.end_date;
END;
$$ LANGUAGE plpgsql;


-- Fakultet/kafedra kesimida xodimlar statistikasi
CREATE OR REPLACE FUNCTION get_faculty_stats()
RETURNS TABLE (
    faculty_name    VARCHAR,
    department_name VARCHAR,
    total_employees BIGINT,
    main_employees  BIGINT,
    part_time       BIGINT,
    phd_count       BIGINT,
    dsc_count       BIGINT,
    avg_kpi         NUMERIC
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        f.name_uz                                    AS faculty_name,
        d.name_uz                                    AS department_name,
        COUNT(DISTINCT em.employee_id)               AS total_employees,
        COUNT(DISTINCT CASE WHEN et.code = 'MAIN' THEN em.employee_id END)  AS main_employees,
        COUNT(DISTINCT CASE WHEN et.code != 'MAIN' THEN em.employee_id END) AS part_time,
        COUNT(DISTINCT CASE WHEN sd.code = 'PhD' THEN ai.employee_id END)   AS phd_count,
        COUNT(DISTINCT CASE WHEN sd.code = 'DSc' THEN ai.employee_id END)   AS dsc_count,
        ROUND(AVG(kpi.total_score), 1)               AS avg_kpi
    FROM employment em
    JOIN faculties f       ON f.id = em.faculty_id
    LEFT JOIN departments d ON d.id = em.department_id
    JOIN employee_types et ON et.id = em.employee_type_id
    LEFT JOIN academic_info ai ON ai.employee_id = em.employee_id
    LEFT JOIN scientific_degrees sd ON sd.id = ai.scientific_degree_id
    LEFT JOIN kpi_evaluations kpi ON kpi.employee_id = em.employee_id
        AND kpi.period_year = EXTRACT(YEAR FROM NOW())
    WHERE em.employment_status = 'active'
    GROUP BY f.name_uz, d.name_uz
    ORDER BY f.name_uz, d.name_uz;
END;
$$ LANGUAGE plpgsql;


-- ============================================================
--  VAZIRLIK UCHUN ASOSIY KO'RINISH (View)
-- ============================================================
CREATE OR REPLACE VIEW v_employees_full AS
SELECT
    e.employee_code,
    e.last_name || ' ' || e.first_name || ' ' || COALESCE(e.middle_name, '') AS full_name,
    e.birth_date,
    EXTRACT(YEAR FROM AGE(e.birth_date))::INT                                  AS age,
    e.gender,
    e.passport_series,
    e.jshshir,
    e.phone,
    e.email,
    f.name_uz                   AS faculty,
    d.name_uz                   AS department,
    p.name_uz                   AS position,
    et.name_uz                  AS employee_type,
    em.staff_rate,
    em.workload_hours,
    sd.name_uz                  AS scientific_degree,
    st.name_uz                  AS scientific_title,
    c.contract_number,
    c.start_date                AS contract_start,
    c.end_date                  AS contract_end,
    (c.end_date - CURRENT_DATE) AS contract_days_left,
    em.work_start_date,
    em.employment_status,
    e.pension_status,
    e.military_status,
    e.status                    AS employee_status
FROM employees e
LEFT JOIN employment em     ON em.employee_id = e.id AND em.is_primary = TRUE
LEFT JOIN faculties f        ON f.id = em.faculty_id
LEFT JOIN departments d      ON d.id = em.department_id
LEFT JOIN positions p        ON p.id = em.position_id
LEFT JOIN employee_types et  ON et.id = em.employee_type_id
LEFT JOIN academic_info ai   ON ai.employee_id = e.id
LEFT JOIN scientific_degrees sd ON sd.id = ai.scientific_degree_id
LEFT JOIN scientific_titles st  ON st.id = ai.scientific_title_id
LEFT JOIN contracts c        ON c.employee_id = e.id AND c.status = 'active'
WHERE e.status = 'active';

COMMENT ON VIEW v_employees_full IS 'Vazirlik hisobotlari va umumiy statistika uchun asosiy ko''rinish';


-- ============================================================
--  DEMO MA'LUMOTLAR (Test uchun)
-- ============================================================
INSERT INTO employees (
    employee_code, last_name, first_name, middle_name,
    birth_date, gender, passport_series, jshshir,
    phone, email, telegram_id, address, status
) VALUES
    ('EMP-0001', 'Aliyev',    'Alisher',  'Akbarovich',
     '1985-03-15', 'M', 'AB 1234567', '12345678901234',
     '+998901234567', 'aliyev@uni.uz', '@aliyev_a',
     'Toshkent sh., Yunusobod tumani', 'active'),
    ('EMP-0002', 'Rahimova',  'Nilufar',  'Salimovna',
     '1978-07-22', 'F', 'CD 7654321', '43210987654321',
     '+998912345678', 'rahimova@uni.uz', '@rahimova_n',
     'Toshkent sh., Chilonzor tumani', 'active'),
    ('EMP-0003', 'Toshmatov', 'Sardor',   'Hamidovich',
     '1990-11-05', 'M', 'EF 1122334', '56789012345678',
     '+998931234567', 'toshmatov@uni.uz', NULL,
     'Toshkent viloyati, Chirchiq sh.', 'active');


-- ============================================================
--  KOMMENTARLAR (Jadval tavsiflar)
-- ============================================================
COMMENT ON TABLE employees         IS 'Xodimlarning shaxsiy asosiy ma''lumotlari';
COMMENT ON TABLE employment        IS 'Xodimning ish, lavozim va tashkiliy joylashuvi';
COMMENT ON TABLE academic_info     IS 'Ilmiy daraja, unvon va ta''lim ma''lumotlari';
COMMENT ON TABLE contracts         IS 'Mehnat shartnomalar va ularning tarixi';
COMMENT ON TABLE orders            IS 'Buyruqlar reestri (ishga qabul, bo''shatish va h.k.)';
COMMENT ON TABLE vacations         IS 'Ta''til arizalari va tasdiqlash jarayoni';
COMMENT ON TABLE vacation_balances IS 'Xodimlarning ta''til balansi (yillik hisobi)';
COMMENT ON TABLE attendance        IS 'Kunlik davomat jurnali';
COMMENT ON TABLE staff_schedule    IS 'Tasdiqlangan shtatlar jadvali';
COMMENT ON TABLE vacancies         IS 'Ochiq vakansiyalar va e''lonlar';
COMMENT ON TABLE documents         IS 'Xodim hujjatlarining elektron arxivi';
COMMENT ON TABLE kpi_evaluations   IS 'Choraklik KPI baholash natijalari';
COMMENT ON TABLE notifications     IS 'Tizim va Telegram xabarnomalar';
COMMENT ON TABLE audit_log         IS 'Barcha o''zgarishlarning to''liq audit jurnali';

-- ============================================================
--  MUVAFFAQIYATLI YARATILDI
-- ============================================================
DO $$
BEGIN
    RAISE NOTICE '============================================';
    RAISE NOTICE '  OTM HR Database — muvaffaqiyatli yaratildi';
    RAISE NOTICE '  Jadvallar: 20 ta';
    RAISE NOTICE '  Indekslar: 17 ta';
    RAISE NOTICE '  Funksiyalar: 3 ta';
    RAISE NOTICE '  Ko''rinishlar (View): 1 ta';
    RAISE NOTICE '============================================';
END;
$$;
