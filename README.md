# Lab-5-TSPP
using System;
using System.Collections.Generic;
using System.IO;
class Entity
{
    public string Name { get; set; }

    public Entity(string name)
    {
        Name = name;
    }
}
class Doctor : Entity
{
    public int Experience { get; set; }

    public Doctor(string name, int experience) : base(name)
    {
        Experience = experience;
    }
}
class Appointment : Entity
{
    public string Day { get; set; }
    public int Visitors { get; set; }
    public string Comment { get; set; }

    public Appointment(string name, string day, int visitors, string comment) : base(name)
    {
        Day = day;
        Visitors = visitors;
        Comment = comment;

    }
}

class Program
{
    static void Main(string[] args)
    {
        string databaseFile = "database.txt";
        List<Entity> entities = LoadDataFromDatabase(databaseFile);
   
        Console.WriteLine("Меню:");
        Console.WriteLine("  1. Додати запис");
        Console.WriteLine("  2. Редагувати запис");
        Console.WriteLine("  3. Видалити запис");
        Console.WriteLine("  4. Показати всi записи");
        Console.WriteLine("  5. Виконати  завдання");
        Console.WriteLine("  6. Вийти");
        Console.WriteLine("  7. Очистити");
        bool exit = false;
        while (!exit)
        {
            Console.Write("Введiть номер пункту меню: ");
            string choice = Console.ReadLine();

            switch (choice)
            {
                case "1":
                    AddRecord(entities);
                    SaveDataToDatabase(databaseFile, entities);
                    break;
                case "2":
                    EditRecord(entities);
                    SaveDataToDatabase(databaseFile, entities);
                    break;
                case "3":
                    DeleteRecord(entities);
                    SaveDataToDatabase(databaseFile, entities);
                    break;
                case "4":
                    ShowRecords(entities);
                    break;
                case "5":
                    PerformIndividualTask(entities);
                    break;
                case "6":
                    exit = true;
                    break;
                case "7":
                    Console.Clear();
                    break;
                default:
                    Console.WriteLine("Невірний номер пункту меню.");
                    break;
            }

            Console.WriteLine();
        }

        Console.WriteLine("До побачення!");
    }

    static List<Entity> LoadDataFromDatabase(string databaseFile)
    {
        List<Entity> entities = new List<Entity>();

        if (File.Exists(databaseFile))
        {
            try
            {
                string[] lines = File.ReadAllLines(databaseFile);
                foreach (string line in lines)
                {
                    string[] parts = line.Split(';');

                    if (parts.Length >= 2)
                    {
                        string entityType = parts[0];
                        string name = parts[1];

                        if (entityType == "Doctor" && parts.Length >= 3)
                        {
                            int experience = int.Parse(parts[2]);
                            entities.Add(new Doctor(name, experience));
                        }
                        else if (entityType == "Appointment" && parts.Length >= 5)
                        {
                            string day = parts[2];
                            int visitors = int.Parse(parts[3]);
                            string comment = parts[4];
                            entities.Add(new Appointment(name, day, visitors, comment));
                        }
                    }
                }
                Console.WriteLine("Данi з файлу завантажено успiшно.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Помилка під час завантаження даних: {ex.Message}");
            }
        }
        else
        {
            Console.WriteLine("Файл бази даних не знайдено. Нова база даних буде створена.");
        }

        return entities;
    }

    static void SaveDataToDatabase(string databaseFile, List<Entity> entities)
    {
        try
        {
            List<string> lines = new List<string>();

            foreach (Entity entity in entities)
            {
                if (entity is Doctor doctor)
                {
                    lines.Add($"Doctor;{doctor.Name};{doctor.Experience}");
                }
                else if (entity is Appointment appointment)
                {
                    lines.Add($"Appointment;{appointment.Name};{appointment.Day};{appointment.Visitors};{appointment.Comment}");
                }
            }

            File.WriteAllLines(databaseFile, lines);

            Console.WriteLine("Дані успішно збережено у файл бази даних.");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Помилка під час збереження даних: {ex.Message}");
        }
    }

    static void AddRecord(List<Entity> entities)
    {
        Console.WriteLine("Додавання нового запису.");

        Console.Write("Введшть тип запису (Doctor або Appointment): ");
        string entityType = Console.ReadLine();

        Console.Write("Введiть iм'я: ");
        string name = Console.ReadLine();

        if (entityType == "Doctor")
        {
            Console.Write("Введiшть стаж: ");
            if (int.TryParse(Console.ReadLine(), out int experience))
            {
                entities.Add(new Doctor(name, experience));
                Console.WriteLine("Запис успiшно додано.");
            }
            else
            {
                Console.WriteLine("Невірне значення для стажу. Запис не був доданий.");
            }
        }
        else if (entityType == "Appointment")
        {
            Console.Write("Введiть день: ");
            string day = Console.ReadLine();

            Console.Write("Введiть кiлькiсть вiдвiдувачiв: ");
            if (int.TryParse(Console.ReadLine(), out int visitors))
            {
                Console.Write("Введіть коментар: ");
                string comment = Console.ReadLine();

                entities.Add(new Appointment(name, day, visitors, comment));
                Console.WriteLine("Запис успішно додано.");
            }
            else
            {
                Console.WriteLine("Невірне значення для кількості відвідувачів. Запис не був доданий.");
            }
        }
        else
        {
            Console.WriteLine("Невірний тип запису. Запис не був доданий.");
        }
    }

    static void EditRecord(List<Entity> entities)
    {
        Console.WriteLine("Редагування запису.");

        Console.Write("Введiть порядковий номер запису, який потрiбно редагувати: ");
        if (int.TryParse(Console.ReadLine(), out int recordIndex) && recordIndex >= 0 && recordIndex < entities.Count)
        {
            Entity entity = entities[recordIndex];

            if (entity is Doctor doctor)
            {
                Console.Write("Введiть нове iм'я (поточне значення: {0}): ", doctor.Name);
                string newName = Console.ReadLine();
                doctor.Name = newName;

                Console.Write("Введіть новий стаж (поточне значення: {0}): ", doctor.Experience);
                if (int.TryParse(Console.ReadLine(), out int newExperience))
                {
                    doctor.Experience = newExperience;
                    Console.WriteLine("Запис успішно змінено.");
                }
                else
                {
                    Console.WriteLine("Невірне значення для стажу. Запис не був змінений.");
                }
            }
            else if (entity is Appointment appointment)
            {
                Console.Write("Введiть нове iм'я (поточне значення: {0}): ", appointment.Name);
                string newName = Console.ReadLine();
                appointment.Name = newName;

                Console.Write("Введіть новий день (поточне значення: {0}): ", appointment.Day);
                string newDay = Console.ReadLine();
                appointment.Day = newDay;

                Console.Write("Введіть нову кількість відвідувачів (поточне значення: {0}): ", appointment.Visitors);
                if (int.TryParse(Console.ReadLine(), out int newVisitors))
                {
                    appointment.Visitors = newVisitors;

                    Console.Write("Введіть новий коментар (поточне значення: {0}): ", appointment.Comment);
                    string newComment = Console.ReadLine();
                    appointment.Comment = newComment;

                    Console.WriteLine("Запис успішно змінено.");
                }
                else
                {
                    Console.WriteLine("Невірне значення для кількості відвідувачів. Запис не був змінений.");
                }
            }
            else
            {
                Console.WriteLine("Невідомий тип запису. Запис не був змінений.");
            }
        }
        else
        {
            Console.WriteLine("Невірний порядковий номер запису.");
        }
    }

    static void DeleteRecord(List<Entity> entities)
    {
        Console.WriteLine("Видалення запису.");

        Console.Write("Введіть порядковий номер запису, який потрібно видалити: ");
        if (int.TryParse(Console.ReadLine(), out int recordIndex) && recordIndex >= 0 && recordIndex < entities.Count)
        {
            entities.RemoveAt(recordIndex);
            Console.WriteLine("Запис успішно видалено  ");

        }
        else
        {
            Console.WriteLine("Невірний порядковий номер запису.");
        }
    }

    static void ShowRecords(List<Entity> entities)
    {
        Console.WriteLine("Записи в базі даних:");

        for (int i = 0; i < entities.Count; i++)
        {
            Entity entity = entities[i];

            if (entity is Doctor doctor)
            {
                Console.WriteLine("{0}. Лiкар: {1}, Стаж: {2}", i, doctor.Name, doctor.Experience);
            }
            else if (entity is Appointment appointment)
            {
                Console.WriteLine("{0}. Прийом: {1}, День: {2}, Вiдвiдувачi: {3}, Коментар: {4}",
                    i, appointment.Name, appointment.Day, appointment.Visitors, appointment.Comment);
            }
            else
            {
                Console.WriteLine("{0}. Невідомий тип запису", i);
            }
        }
    }

    static void PerformIndividualTask(List<Entity> entities)
    {
        Console.WriteLine("Середня кiлькiсть вiдвiдувачiв, прийом з мiнiмальною кiлькiстю вiдвiдувачiв, найдовшим коментарем ");



        int totalVisitors = 0;
        int minVisitors = int.MaxValue;
        string longestComment = "";

        foreach (Entity entity in entities)
        {
            if (entity is Appointment appointment)
            {
                totalVisitors += appointment.Visitors;

                if (appointment.Visitors < minVisitors)
                {
                    minVisitors = appointment.Visitors;
                }

                if (appointment.Comment.Length > longestComment.Length)
                {
                    longestComment = appointment.Comment;
                }
            }
        }

        if (entities.Count > 0)
        {
            double averageVisitors = (double)totalVisitors / entities.Count;
            Console.WriteLine("Середня кiлькiсть вiдвiдувачiв: {0}", averageVisitors);
            Console.WriteLine("Прийом з мiнiмальною кiлькiстю вiдвiдувачiв: {0}", minVisitors);
            Console.WriteLine("Прийом з найдовшим коментарем: {0}", longestComment);
        }
        else
        {
            Console.WriteLine("База даних порожня. Виконання завдання неможливе.");
        }
    }
}
