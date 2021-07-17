  #### create functions 
  
        create function concat_lower_or_upper(a text ,b text ,uppercase boolean DEFAULT false)
        returns text
        as $$ 
        select case when $3 then upper($1 || ' ' || $2)
        else lower ($1 || ' ' || $2)
        $$
        language sql immutable strict;
        
         
        select concat_lower_or_upper('Hello','namatullah',true)