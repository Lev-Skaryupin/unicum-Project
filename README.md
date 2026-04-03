import random
import math
from fractions import Fraction


class MathTrainer:
    def __init__(self):
        self.score = 0
        self.total_questions = 0

    def show_menu(self):
        print("\n" + "=" * 50)
        print("                 MathOperation       ")
        print("=" * 50)
        print("1. Лёгкий уровень (Числа 0-9)")
        print("2. Средний уровень (Числа 10-100)")
        print("3. Сложный уровень (Дроби)")
        print("4. Показать статистику")
        print("5. Выход")
        print("-" * 50)

    def get_difficulty(self):
        while True:
            try:
                choice = input("Выберите уровень сложности (1-5): ")
                if choice in ['1', '2', '3', '4', '5']:
                    return choice
                else:
                    print("Пожалуйста, введите число от 1 до 5!")
            except:
                print("Неверный ввод!")

    def easy_level(self):
        """Уровень 1: числа от 0 до 9"""
        num1 = random.randint(0, 9)
        num2 = random.randint(0, 9)
        operation = random.choice(['+', '-', '*'])

        if operation == '-':
            num1, num2 = max(num1, num2), min(num1, num2)

        return num1, num2, operation

    def medium_level(self):
        """Уровень 2: числа от 10 до 100"""
        num1 = random.randint(10, 100)
        num2 = random.randint(10, 100)
        operation = random.choice(['+', '-', '*'])

        if operation == '-':
            num1, num2 = max(num1, num2), min(num1, num2)

        return num1, num2, operation

    def hard_level(self):
        """Уровень 3: дроби (обычные, десятичные, смешанные)"""
        fraction_type = random.choice(['simple', 'decimal', 'mixed'])

        if fraction_type == 'simple':
            num1 = Fraction(random.randint(1, 10), random.randint(2, 10))
            num2 = Fraction(random.randint(1, 10), random.randint(2, 10))
            operation = random.choice(['+', '-', '*', '/'])
            return num1, num2, operation, 'simple'

        elif fraction_type == 'decimal':
            num1 = round(random.uniform(0.1, 10.0), 2)
            num2 = round(random.uniform(0.1, 10.0), 2)
            operation = random.choice(['+', '-', '*', '/'])
            return num1, num2, operation, 'decimal'

        else:
            whole1 = random.randint(1, 5)
            frac1 = Fraction(random.randint(1, 5), random.randint(2, 6))
            num1 = whole1 + frac1

            whole2 = random.randint(1, 5)
            frac2 = Fraction(random.randint(1, 5), random.randint(2, 6))
            num2 = whole2 + frac2

            operation = random.choice(['+', '-', '*', '/'])
            return num1, num2, operation, 'mixed'

    def calculate_result(self, num1, num2, operation):
        """Вычисление результата операции"""
        if operation == '+':
            return num1 + num2
        elif operation == '-':
            return num1 - num2
        elif operation == '*':
            return num1 * num2
        elif operation == '/':
            if num2 != 0:
                return num1 / num2
            else:
                return None
        return None

    def format_question(self, num1, num2, operation, level_type=None):
        """Форматирование вопроса для отображения"""
        if level_type == 'simple':
            return f"{num1.numerator}/{num1.denominator} {operation} {num2.numerator}/{num2.denominator}"
        elif level_type == 'mixed':
            if isinstance(num1, Fraction):
                whole1 = num1.numerator // num1.denominator
                remainder1 = num1.numerator % num1.denominator
                if whole1 > 0:
                    num1_str = f"{whole1} {remainder1}/{num1.denominator}" if remainder1 > 0 else f"{whole1}"
                else:
                    num1_str = f"{num1.numerator}/{num1.denominator}"

                whole2 = num2.numerator // num2.denominator
                remainder2 = num2.numerator % num2.denominator
                if whole2 > 0:
                    num2_str = f"{whole2} {remainder2}/{num2.denominator}" if remainder2 > 0 else f"{whole2}"
                else:
                    num2_str = f"{num2.numerator}/{num2.denominator}"

                return f"{num1_str} {operation} {num2_str}"
            return f"{num1} {operation} {num2}"
        else:
            return f"{num1} {operation} {num2}"

    def check_answer(self, user_answer, correct_answer, level_type=None):
        """Проверка ответа с учётом типа дроби"""
        try:
            if level_type == 'simple':
                if '/' in user_answer:
                    num, den = map(int, user_answer.split('/'))
                    user_frac = Fraction(num, den)
                else:
                    user_frac = Fraction(float(user_answer)).limit_denominator()
                return user_frac == correct_answer
            else:
                user_float = float(user_answer)
                return abs(user_float - float(correct_answer)) < 0.01
        except:
            return False

    def run_question(self, difficulty):
        """Запуск одного вопроса. Возвращает: True - правильно, False - неправильно, 'menu' - выход в меню, 'exit' - выход из программы"""
        if difficulty == '1':
            num1, num2, operation = self.easy_level()
            level_type = None
        elif difficulty == '2':
            num1, num2, operation = self.medium_level()
            level_type = None
        else:
            num1, num2, operation, level_type = self.hard_level()

        correct_answer = self.calculate_result(num1, num2, operation)

        if correct_answer is None:
            return False

        question = self.format_question(num1, num2, operation, level_type)
        print(f"\nВопрос {self.total_questions + 1}: {question} = ?")

        user_input = input("Ваш ответ: ").strip().lower()

        if user_input == 'menu':
            return 'menu'
        elif user_input == 'exit':
            return 'exit'

        if self.check_answer(user_input, correct_answer, level_type):
            print("✅ Правильно! +1 балл")
            self.score += 1
            return True
        else:
            if level_type == 'simple':
                correct_frac = Fraction(correct_answer).limit_denominator()
                print(f"❌ Неправильно! Правильный ответ: {correct_frac}")
            elif level_type == 'mixed':
                if isinstance(correct_answer, Fraction):
                    if correct_answer.numerator > correct_answer.denominator:
                        whole = correct_answer.numerator // correct_answer.denominator
                        remainder = correct_answer.numerator % correct_answer.denominator
                        if remainder > 0:
                            print(f"❌ Неправильно! Правильный ответ: {whole} {remainder}/{correct_answer.denominator}")
                        else:
                            print(f"❌ Неправильно! Правильный ответ: {whole}")
                    else:
                        print(f"❌ Неправильно! Правильный ответ: {correct_answer}")
                else:
                    print(f"❌ Неправильно! Правильный ответ: {correct_answer:.2f}")
            else:
                # Форматирование для обычных чисел
                if isinstance(correct_answer, float) and correct_answer.is_integer():
                    print(f"❌ Неправильно! Правильный ответ: {int(correct_answer)}")
                else:
                    print(f"❌ Неправильно! Правильный ответ: {correct_answer}")
            return False

    def show_statistics(self):
        """Показать статистику"""
        print("\n" + "=" * 50)
        print("          СТАТИСТИКА")
        print("=" * 50)
        if self.total_questions > 0:
            percentage = (self.score / self.total_questions) * 100
            print(f"Всего вопросов: {self.total_questions}")
            print(f"Правильных ответов: {self.score}")
            print(f"Процент успеха: {percentage:.1f}%")

            if percentage >= 80:
                print("⭐ Отлично! Вы математический гений!")
            elif percentage >= 60:
                print("👍 Хорошо! Но можно и лучше!")
            else:
                print("📚 Нужно ещё немного практики!")
        else:
            print("Пока нет решённых вопросов!")
        print("=" * 50)

    def run_level(self, difficulty):
        """Запуск уровня сложности. Возвращает: True - продолжить, False - выход из программы"""
        level_names = {'1': 'ЛЁГКИЙ', '2': 'СРЕДНИЙ', '3': 'СЛОЖНЫЙ'}
        print(f"\n{'=' * 50}")
        print(f"     УРОВЕНЬ {level_names[difficulty]}")
        print(f"{'=' * 50}")
        print("В любой момент вместо ответа введите:")
        print("  'menu' - вернуться в главное меню")
        print("  'exit' - выйти из программы")
        print("-" * 50)

        questions_in_level = 0
        while True:
            try:
                result = self.run_question(difficulty)

                if result == 'menu':
                    print("\nВозврат в главное меню...")
                    return True
                elif result == 'exit':
                    print("\nВыход из программы...")
                    return False
                elif result is True:
                    self.total_questions += 1
                    questions_in_level += 1
                elif result is False:
                    self.total_questions += 1
                    questions_in_level += 1

                if questions_in_level > 0 and questions_in_level % 5 == 0:
                    print(f"\n--- Пройдено {questions_in_level} вопросов в этом уровне ---")
                    choice = input("Продолжить? (да/нет/menu/exit): ").lower().strip()
                    if choice in ['нет', 'no', 'n']:
                        print("\nВозврат в главное меню...")
                        return True
                    elif choice == 'menu':
                        print("\nВозврат в главное меню...")
                        return True
                    elif choice == 'exit':
                        print("\nВыход из программы...")
                        return False
                    # Иначе продолжаем

            except KeyboardInterrupt:
                print("\n\nВозврат в меню...")
                return True
            except Exception as e:
                print(f"Ошибка: {e}")
                continue

        return True


def main():
    trainer = MathTrainer()
    running = True

    while running:
        trainer.show_menu()
        choice = trainer.get_difficulty()

        if choice == '1':
            result = trainer.run_level('1')
            if result is False:
                running = False
        elif choice == '2':
            result = trainer.run_level('2')
            if result is False:
                running = False
        elif choice == '3':
            result = trainer.run_level('3')
            if result is False:
                running = False
        elif choice == '4':
            trainer.show_statistics()
            input("\nНажмите Enter для продолжения...")
        elif choice == '5':
            print("\nСпасибо за использование MathOperation!")
            print(f"Ваша финальная статистика: {trainer.score}/{trainer.total_questions}")
            running = False


if __name__ == "__main__":
    main()
