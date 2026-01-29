# OOP: Inheritance, Encapsulation, and Polymorphism

## Код программы

```python

class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}
    
    def rate_lecture(self, lecturer, course, grade):
        if not isinstance(lecturer, Lecturer):
            return 'Ошибка'
        if course not in self.courses_in_progress:
            return 'Ошибка'
        if course not in lecturer.courses_attached:
            return 'Ошибка'
        if not (1 <= grade <= 10):
            return 'Ошибка'
        
        if course in lecturer.grades:
            lecturer.grades[course].append(grade)
        else:
            lecturer.grades[course] = [grade]
        return None
    
    def _average_grade(self):
        all_grades = []
        for grades in self.grades.values():
            all_grades.extend(grades)
        return sum(all_grades) / len(all_grades) if all_grades else 0
    
    def __str__(self):
        avg = self._average_grade()
        courses_in_progress = ', '.join(self.courses_in_progress)
        finished_courses = ', '.join(self.finished_courses)
        return (f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за домашние задания: {avg:.1f}\n'
                f'Курсы в процессе изучения: {courses_in_progress}\n'
                f'Завершенные курсы: {finished_courses}')
    
    def __lt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self._average_grade() < other._average_grade()
    
    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self._average_grade() == other._average_grade()


class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []
    
    def __str__(self):
        return f'Имя: {self.name}\nФамилия: {self.surname}'


class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}
    
    def _average_grade(self):
        all_grades = []
        for grades in self.grades.values():
            all_grades.extend(grades)
        return sum(all_grades) / len(all_grades) if all_grades else 0
    
    def __str__(self):
        avg = self._average_grade()
        return (f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за лекции: {avg:.1f}')
    
    def __lt__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self._average_grade() < other._average_grade()
    
    def __eq__(self, other):
        if not isinstance(other, Lecturer):
            return NotImplemented
        return self._average_grade() == other._average_grade()

class Reviewer(Mentor):
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and
         course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course] += [grade]
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

def average_grade_for_course(students, course_name):
     all_grades = []
    for student in students:
        if course_name in student.grades:
            all_grades.extend(student.grades[course_name])
    return sum(all_grades) / len(all_grades) if all_grades else 0


def average_grade_for_lecturers(lecturers, course_name):
    all_grades = []
    for lecturer in lecturers:
        if course_name in lecturer.grades:
            all_grades.extend(lecturer.grades[course_name])
    return sum(all_grades) / len(all_grades) if all_grades else 0

student1 = Student('Алиса', 'Смирнова', 'Ж')
student1.courses_in_progress = ['Python', 'Git']
student1.finished_courses = ['Введение в программирование']

student2 = Student('Борис', 'Иванов', 'М')
student2.courses_in_progress = ['Python', 'Java']
student2.finished_courses = ['Алгоритмы']

lecturer1 = Lecturer('Иван', 'Петров')
lecturer1.courses_attached = ['Python', 'Git']

lecturer2 = Lecturer('Мария', 'Сидорова')
lecturer2.courses_attached = ['Python', 'Java']

reviewer1 = Reviewer('Анна', 'Васильева')
reviewer1.courses_attached = ['Python', 'Git']

reviewer2 = Reviewer('Дмитрий', 'Козлов')
reviewer2.courses_attached = ['Python', 'Java']

student1.rate_lecture(lecturer1, 'Python', 9)
student1.rate_lecture(lecturer1, 'Git', 8)
student2.rate_lecture(lecturer2, 'Python', 10)
student2.rate_lecture(lecturer2, 'Java', 9)
reviewer1.rate_hw(student1, 'Python', 10)
reviewer1.rate_hw(student1, 'Git', 9)
reviewer2.rate_hw(student2, 'Python', 8)
reviewer2.rate_hw(student2, 'Java', 10)

print("=== СТУДЕНТ 1 ===")
print(student1)
print("\n=== СТУДЕНТ 2 ===")
print(student2)
print("\n=== ЛЕКТОР 1 ===")
print(lecturer1)
print("\n=== ЛЕКТОР 2 ===")
print(lecturer2)
print("\n=== РЕЦЕНЗЕНТ 1 ===")
print(reviewer1)
print("\n=== РЕЦЕНЗЕНТ 2 ===")
print(reviewer2)

print(f"\nСравнение студентов: Алиса < Борис? {student1 < student2}")
print(f"Сравнение лекторов: Иван < Мария? {lecturer1 < lecturer2}")

print(f"\nСредняя оценка за ДЗ по Python: {average_grade_for_course([student1, student2], 'Python'):.1f}")
print(f"Средняя оценка за лекции по Python: {average_grade_for_lecturers([lecturer1, lecturer2], 'Python'):.1f}")