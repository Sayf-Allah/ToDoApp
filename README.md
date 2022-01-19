# ToDoApp
# Overview
The goal of the project is to create an application to manage our tasks. It should have all the features of main application such as menues, actions and toolbar. The application must store an archive of all the pending and finished tasks.
# the application interface
first we are going to use Qt Designer to design our application

# Defining a Task
now we need to create a dialog on wich we are going to define the tasks
- A Task is defined by the following attributes:
- A description: stating the text and goal for the task like (Buying the milk).

- A finished boolean indicating if the task is Finished or due.
- A Tag category to show the class of the task which is reduced to the following values:

  - Work
  - Life
  - Other
- Finally, a task should have a DueDate which stores the Date planned for the date.

we used qt designer to create the dialog

now , we will add setters and a function to gettext from the dialog
- date setter 
```cpp
void addTaskDialog::setdate(int y,int m, int d){
    QDate da;

    da.setDate(y,m,d);
    ui->dateEdit->setDate(da);
}
```
- description setter
```cpp
void addTaskDialog::setdesc(QString a){
    ui->lineEdit->setText(a);
}
```
- tag setter
```cpp
void addTaskDialog::settag(QString a){
    ui->comboBox->setCurrentText(a);
}
```
- checkbox setter
```cpp
void addTaskDialog::setf(bool a){
    ui->checkBox->setChecked(a);
}
```
- gettext function
```cpp
QString addTaskDialog::getText(){

    QString a= ui->lineEdit->text() + " Due : " + ui->dateEdit->text() + "  Tag : " + ui->comboBox->currentText() +"";
    return a;
}
```
we need a function to check if the checkbox is checked
```cpp
bool addTaskDialog::isChecked(){
    if(ui->checkBox->isChecked()){
        return true;
    }
    return false;
}
```
we need also to set a minimum date
```cpp
    QDate date =QDate::currentDate();
    ui->dateEdit->setMinimumDate(date);
    ui->dateEdit->setDate(date);
```
