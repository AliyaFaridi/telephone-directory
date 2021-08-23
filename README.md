# telephone-directory
import sys,sqlite3,re

class telephone:

    def __init__(self):

        self.conn = sqlite3.connect('telephone.db')
        self.c = self.conn.cursor()
        self.c.execute('create table if not exists record(name text, phone_no int, email text, address text)')

    def add_contact(self):
        name = input('enter name:')
        while True:
            ph_no = int(input('enter no:'))
            if len(str(ph_no))==10:
                break
            print('enter a valid number')
        while True:
            email_id = input('enter email_id:')#\w+@\w+.\w+
            m = re.search('\w+@\w+.\w+',email_id)
            if m is not None:
               break
            print('invalid id')
        
        address = input('enter address:')

        self.c.execute('insert into record values(?,?,?,?)',(name, ph_no,email_id,address))
        self.conn.commit()

    def display(self):
        self.__init__()
        self.c.execute('select * from record')
        data = self.c.fetchall()
        print('{}\t{}\t\t{}\t\t\t{}'.format('name','ph_no','email','address'))
        print('------------------------------------------------------')
        for i in  data:
            print('{}\t{}\t{}\t\t{}'.format(i[0],i[1],i[2],i[3]))
            print('------------------------------------------------------')
        

    def screen(self):
        print('1:add contact')
        print('2:display all contacts')
        print('3:search for a contact')
        print('4:delete')
        print('5:update')
        print('6:exit')

        c=int(input('enter choice:'))
        return c
    
    def search(self):
        name = input('enter name:')
        self.c.execute('select * from record where name=?',(name,))
        d= self.c.fetchall()
        print('{}\t{}\t\t{}\t\t\t{}'.format('name','ph_no','email','address'))
        for i in  d:
            print('{}\t{}\t{}\t\t{}'.format(i[0],i[1],i[2],i[3]))
            print('------------------------------------------')

    def delete(self):
        name = input('enter name:')
        address = input('enter address:')
        self.c.execute('delete from record where name=?',(name,))
        self.conn.commit()

    def update(self):
        name = input('enter name:')
        self.c.execute('select * from record where name=?',(name,))
        d = self.c.fetchall()
        if d is not None:
            print('1:change ph_no:')
            print('2:change address:')

            n = int(input('enter choice:'))
            if n==1:
                new_ph_no = int(input('enter ph_no'))
                self.c.execute('update record set ph_no = ? where name = ?',(new_ph_no,name))
            elif n==2:
                new_add = input('enter address')
                self.c.execute('update record set address = ? where name = ?',(new_add,name))

            self.conn.commit()
        else:
            print('not found')

    
tel = telephone()
while True:
    m=tel.screen()
    if m==1:
        tel.add_contact()
    elif m==2:
        tel.display()
    elif m==3:
        tel.search()
    elif m==4:
        tel.delete()
    elif m==5:
        tel.update()
    else:
        tel.conn.close()
        sys.exit()
