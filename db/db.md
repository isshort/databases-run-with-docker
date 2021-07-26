  #### create functions 
  
        create function concat_lower_or_upper(a text ,b text ,uppercase boolean DEFAULT false)
        returns text
        as $$ 
        select case when $3 then upper($1 || ' ' || $2)
        else lower ($1 || ' ' || $2)
        $$
        language sql immutable strict;
        
         
        select concat_lower_or_upper('Hello','namatullah',true)
# create function with ENUM type      
 ### create type 
 
 
     CREATE TYPE rainbow AS ENUM ('red','orange','yellow','green','blue','purple')

     select enum_range(null::rainbow)

     CREATE TABLE my_colors(color rainbow,note TEXT);

     insert into my_colors values('red','some descriptions')
### create function

    CREATE OR REPLACE FUNCTION get_color_note(rainbow) RETURNS TEXT AS 
    'SELECT  note from my_colors where color= $1'
    LANGUAGE SQL;

    select get_color_note('red')
