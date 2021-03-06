[![CircleCI](https://circleci.com/gh/bakkenbaeck/bbtornado/tree/master.svg?style=svg&circle-token=541d1c231d77b2fc0e9ed3091cb30e4cf2378043)](https://circleci.com/gh/bakkenbaeck/bbtornado/tree/master)

A collection of utilities for writing REST backends with Tornado and SQLAlchemy

This is all tailored to work with Bakken & Bæcks internal frameworks, it will not all make sense outside of it.

This code should work for both python 2.7 and 3.6.

## Example

```Python

import bbtornado.main

from bbtornado.web import Application
from bbtornado.models import Base, BaseModel
from bbtornado.handlers import BaseHandler

from sqlalchemy import Column, types

class User(Base, BaseModel):
    __tablename__ = 'users'
    id = Column(types.Integer, primary_key=True, autoincrement=True)
    visits = Column(types.Integer, default=0)

class MainHandler(BaseHandler):
    def get(self):
        if self.current_user == None:
            user = User()
            self.db.add(user)
            self.db.commit()
            print('created user', user.id)
            self.current_user = user.id
        else:
            user = self.db.query(User).get(self.current_user)
        self.write("Hello %s!  You've been here %s times before!" % (user.id, user.visits))
        user.visits += 1
        self.db.add(user)
        self.db.commit()
        self.finish()

if __name__ == '__main__':
    bbtornado.main.setup()
    app = Application([
        (r"/", MainHandler)
    ])
    bbtornado.main.main(app)
```
