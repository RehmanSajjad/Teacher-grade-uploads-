# Teacher-grade-uploads-import csv
import sqlite3

class GradeBookManager:
    def __init__(self, db_name='grades.db'):
        self.db_name = db_name

    def read_grades_from_csv(self, file_name):
        try:
            with open(file_name, 'r') as file:
                csv_reader = csv.reader(file)
                grades = [row for row in csv_reader]
            return grades
        except FileNotFoundError:
            print("File not found.")

    def upload_grades_to_database(self, grades):
        try:
            conn = sqlite3.connect(self.db_name)
            cursor = conn.cursor()

            cursor.execute('''CREATE TABLE IF NOT EXISTS teacher_grades (
                                student_id INTEGER PRIMARY KEY,
                                student_name TEXT,
                                grade INTEGER
                            )''')

            for row in grades:
                cursor.execute("INSERT INTO teacher_grades (student_id, student_name, grade) VALUES (?, ?, ?)", row)
            
            conn.commit()
            conn.close()
            print("Grades uploaded to the database successfully!")
        except sqlite3.Error as e:
            print(f"Error uploading grades: {e}")

# Example usage of the GradeBookManager module
if __name__ == "__main__":
    grade_book = GradeBookManager()  # Creating an instance of the GradeBookManager

    # Replace 'teacher_grades.csv' with your file name
    file_name = 'teacher_grades.csv'
    grades_data = grade_book.read_grades_from_csv(file_name)

    if grades_data:
        grade_book.upload_grades_to_database(grades_data)
