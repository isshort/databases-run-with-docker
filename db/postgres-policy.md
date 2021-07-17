### create table 
    
        create table passwd(
        user_name text unique not null,
        pwhash text,
        uid int primary key,
        gid int not null,
        real_name text not null,
        home_phone text,
        extra_info text,
        home_dir text not null,
        shell text not null
        )
### Create Role

        create role admin;  -- Adminstrator
        create role bob;    -- Normal User
        create role alice;  -- Normal User
### Insert Datas

        insert into passwd values('admin','xxx',0,0,'Admin','111-222-333',null,'/root','/bin/dash');
        insert into passwd values('bob','xxx',1,1,'Bob','111-345-333',null,'/home/bob','/bin/zsh');
        insert into passwd values('alice','xxx',2,2,'Alice','1343-222-333',null,'/home/alice','/bin/zsh');

-- Be sure to enable row-level security on the table
    
        alter table passwd enable row level security;

### create policies
    -- Adminstrator can see all rows and add any rows 
        create policy admin_all on passwd to admin using (true) with check (true);

    -- Normal Users can view all rows
        create policy view_all on passwd for select using (true);

    -- Normal users can update their own records, but 
    -- limit which shells a normal user is allowed for update
        create policy user_mod on passwd for update using(current_user = user_name)
        with check (current_user = user_name AND shell IN ('/bin/bash','/bin/sh','/bin/dash','/bin/zsh','/bin/tcsh'));


-- Allow admin all normal rights 
    
        GRANT SELECT,INSERT,UPDATE,DELETE on passwd to admin;

-- Users only get select access on public columns 
       
        GRANT SELECT (user_name ,uid,gid,real_name,home_phone,extra_info,home_dir,shell) on passwd to public;


-- allow users to update ceretian columns 
       
        GRANT UPDATE (pwhash,real_name,home_phone,extra_info,shell) on passwd to public 

## Check with postgres terminal

    -- -- admin can view all rows and fields
    -- postgres=> set role admin;
    -- SET
    -- postgres=> table passwd;
    --  user_name | pwhash | uid | gid | real_name | home_phone |
    --  extra_info | home_dir | shell
    -- -----------+--------+-----+-----+-----------+--------------
    -- +------------+-------------+-----------
    --  admin | xxx | 0 | 0 | Admin | 111-222-3333 |
    --  | /root | /bin/dash
    --  bob | xxx | 1 | 1 | Bob | 123-456-7890 |
    --  | /home/bob | /bin/zsh
    --  alice | xxx | 2 | 1 | Alice | 098-765-4321 |
    --  | /home/alice | /bin/zsh
    -- (3 rows)
    -- -- Test what Alice is able to do
    -- postgres=> set role alice;
    -- SET
    -- postgres=> table passwd;
    -- ERROR: permission denied for relation passwd
    -- postgres=> select
    --  user_name,real_name,home_phone,extra_info,home_dir,shell from
    --  passwd;
    --  user_name | real_name | home_phone | extra_info | home_dir |
    --  shell
    -- -----------+-----------+--------------+------------+-------------
    -- +-----------
    --  admin | Admin | 111-222-3333 | | /root
    --  | /bin/dash
    --  bob | Bob | 123-456-7890 | | /home/bob
    --  | /bin/zsh
    --  alice | Alice | 098-765-4321 | | /home/alice
    --  | /bin/zsh
    -- (3 rows)
    -- postgres=> update passwd set user_name = 'joe';
    -- ERROR: permission denied for relation passwd
    -- -- Alice is allowed to change her own real_name, but no others
    -- postgres=> update passwd set real_name = 'Alice Doe';
    -- 79
    -- Data Definition
    -- UPDATE 1
    -- postgres=> update passwd set real_name = 'John Doe' where user_name
    --  = 'admin';
    -- UPDATE 0
    -- postgres=> update passwd set shell = '/bin/xx';
    -- ERROR: new row violates WITH CHECK OPTION for "passwd"
    -- postgres=> delete from passwd;
    -- ERROR: permission denied for relation passwd
    -- postgres=> insert into passwd (user_name) values ('xxx');
    -- ERROR: permission denied for relation passwd
    -- -- Alice can change her own password; RLS silently prevents
    --  updating other rows
    -- postgres=> update passwd set pwhash = 'abc';
    -- UPDATE 1