```csharp
using MySql.Data.MySqlClient;
using System;
using System.Collections.Generic;

namespace competition
{
    public class DBConnection
    {
        static string server = "127.0.0.1";
        static int port = 3306;
        static string user = "root";
        static string password = "root";
        static string database = "test";

        public static MySqlConnection connect()
        {
            Console.WriteLine("连接 mysql 数据库");
            string connStr = $"server={server};port={port};user={user};password={password};database={database};";
            return new MySqlConnection(connStr);
        }

        public static List<User> Select()
        {
            string sql = "select * from employee";
            MySqlConnection conn = connect();
            conn.Open();
            MySqlCommand cmd = new MySqlCommand(sql, conn);
            MySqlDataReader reader = cmd.ExecuteReader();

            List<User> result = new List<User>();
            while (reader.Read())
            {
                User user = new User();
                user.Name = reader.GetString("name");
                user.Sex = reader.GetString("sex");
                user.Age = reader.GetInt32("age");
                user.Marry = reader.GetString("marry");
                user.Education = reader.GetString("education");
                result.Add(user);
            }
            conn.Close();
            return result;
        }
    }
}

```

