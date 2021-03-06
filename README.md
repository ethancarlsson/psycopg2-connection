<h1>
    Psycopg2 Connection
</h1>
<p>
    Psycopg2 Connection is a very light module which makes some abstractions on top of the psycopg2 package.
    The goal is to help you write SQL code right into your python program, without needing to worry about opening and closing connections to postgres. 
</p>
<h2>
    What does it do?
</h2>
<p>
    It makes it easier and quicker to fetch, execute and commmit to a postgres table. Also has methods to help copy to and from IO like files. 
</p>

<h2>
    Why and when to use this?
</h2>
<p>
    This package is for projects where you want to work directly with postgres, but you don't want to be openning and closing database connections within your code. 
</p>
<h2>
    How do you use it?
</h2>
<p>
    Import the PgDbConnector, Create a class that inherits from it and add your database's information.
</p>
<code>

    from psycopg2-connection import PgDbConnector

    class MyDbConnector(PgDbConnector):
        host = 'my-host'
        dbname = 'my-databases-name'
        user = 'my-username'
        password = 'my-password'

</code>
<p>
    You may find it conventient to place this "connector" in a seperate file and inherit from it. This way your database information can be kept secure.
</p>
<p>
    Now you can begin creating classes to interact with your postgres database. For example:
</p>
<code>

    from io import SringIO
    from other_file import MyDbConnector

    class PgConnectedThing(MyDbConnector):
        def __init__(self):
            sql = 'SELECT some_pk, some_data FROM my_table;'

            self.data = self.fetch_records(sql)

            self.new_table = str()

        def make_new_table(self):
            for row in self.data:
                foreign_key = row[0]
                some_new_data = extract_some_data(row[1])

                self.new_table += f'{foreign_key}\t{some_new_data}\n'

            io_table = StringIO(self.new_table)
            self.copy_from_file(
                io_table,
                'new_table',
                'foreign_key',
                'new_data'
            )
</code>
<p>
    This little example class initiates with data from your postgres table. It then loops over the data, extracts some data from the first row and appends the primary key from the first table and the new data to a string representaiton of a new table. Onse the loop is complete it transforms that string to an StringIO and uses copy_from_file to add the data to a new table. 
</p>
<p>
    It should be noted that we will have opened and closed 2 connections to our database if we call the make_new_table function, but we didn't have to see these connections in the code. The hope is that this way it can feel like we are just writing sql code into our python program, without needing to worry about the connections and without needing to wrap our heads around a whole new framework or ORM.
</p>
