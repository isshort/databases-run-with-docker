## partions tutorials
 Create table 
 
     CREATE TABLE measurement (
       city_id int not null,
       logdate date not null,
       peaktemp int,
       unitsales int
      );
create partitions table

    create table measurement_y2020(check(logdate >= DATE '2019-11-2' AND logdate < DATE '2021-11-2')) INHERITS (measurement);
    create INDEX measurment_y2020_logdate on measurement_y2020 (logdate);

    create table measurement_y2021(check(logdate >= DATE '2020-11-2' AND logdate < DATE '2022-11-2')) INHERITS (measurement);
    create INDEX measurment_y2021_logdate on measurement_y2021 (logdate);

    create table measurement_y2025(check(logdate >= DATE '2022-11-2' AND logdate < DATE '2025-11-2')) INHERITS (measurement);
    create INDEX measurment_y2025_logdate on measurement_y2025 (logdate);


## -- create trigger function

    create or replace function measurement_insert_trigger()
    RETURNS TRIGGER AS $$
    BEGIN 
      INSERT INTO measurement_y2021 VALUES (NEW.*);
      RETURN NULL;
    END;
    $$
    LANGUAGE plpgsql;

##-- after creating the function, we create a trigger which calls the trigger function:

    CREATE TRIGGER insert_measurement_trigger
    BEFORE INSERT ON measurement FOR EACH ROW EXECUTE FUNCTION measurement_insert_trigger();

### Insert value into root table

    INSERT INTO public.measurement(city_id, logdate, peaktemp, unitsales) VALUES (1, '2022-11-1',234, 345);
    INSERT INTO public.measurement(city_id, logdate, peaktemp, unitsales) VALUES (1, '2022-11-1',234, 345);

## Change functions more complex 
-- We can create more complex trigger function

    CREATE OR REPLACE FUNCTION measurement_insert_trigger()
    RETURNS TRIGGER AS $$
    BEGIN
      IF(NEW.logdate >= DATE '2015-2-23' AND NEW.logdate < DATE '2020-2-23') THEN 
        INSERT INTO measurement_y2020 VALUES (NEW.*);
      ELSEIF (NEW.logdate >= DATE '2020-2-23' AND NEW.logdate < DATE '2022-2-23') THEN 
        INSERT INTO measurement_y2021 VALUES (NEW.*);
      ELSEIF (NEW.logdate >= DATE '2022-2-23' AND NEW.logdate < DATE '2025-2-23') THEN 
        INSERT INTO measurement_y2025 VALUES (NEW.*);
      ELSE
        RAISE EXCEPTION 'Date out of range. Fix the measurement_insert_trigger() function!';
      END IF;
      RETURN NULL;
    END;
    $$
    LANGUAGE plpgsql;

Insert values

    INSERT INTO public.measurement(city_id, logdate, peaktemp, unitsales) VALUES (1, '2025-2-1',234, 345);
    INSERT INTO public.measurement(city_id, logdate, peaktemp, unitsales) VALUES (1, '2020-2-1',234, 345);
    INSERT INTO public.measurement(city_id, logdate, peaktemp, unitsales) VALUES (1, '2024-2-1',234, 345);
    INSERT INTO public.measurement(city_id, logdate, peaktemp, unitsales) VALUES (1, '2025-1-1',234, 345);
