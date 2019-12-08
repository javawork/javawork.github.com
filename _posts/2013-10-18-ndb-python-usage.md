---
layout: post
title: "NDB python usage"
description: ""
category: ""
tags: [ndb python usage]
---

### Model class
``` python
class StudentModel(ndb.Model):
	id = ndb.StringProperty()
	name = ndb.StringProperty()
	score = ndb.IntegerProperty()
```

### Add
``` python
def add(self, id, name, score):
	model = StudentModel(
		parent=ndb.Key("student", '*notitle*'),
		id = id,
		name = name,
		score = int(score)
	)
	model.put()
```

### Delete
``` python
def delete(self, id):
	q = StudentModel.query()
	q = q.filter(StudentModel.id == id)
	result = q.fetch(1)
	if len(result) == 0:
		self.response.write('no instance')
		return
	result[0].key.delete()
	self.response.write( 'delete id = %s'%(id) )
```

### Update
``` python
def update(self, id, score_str):
	score = int(score_str)
	q = StudentModel.query()
	q = q.filter(StudentModel.id == id)
	result = q.fetch(1)
	if len(result) == 0:
		self.response.write('no instance')
		return
	result[0].score = score
	result[0].put()
	self.response.write( 'update id = %s'%(id) )
```

### Query
``` python
def query(self, id):
	q = StudentModel.query()
	q = q.filter(StudentModel.id == id)
	result = q.fetch(1)
	if len(result) == 0:
		self.response.write('nothing')
		return
	s = result[0]
	self.response.write('%s %s %d<br/>'%(s.id, s.name, s.score))
```

### Select All
``` python
def list(self):
	q = StudentModel.query()
	result = q.fetch(999999)
	for s in result:
		self.response.write('%s %s %d<br/>'%(s.id, s.name, s.score))
```

### Full source
``` python
class StudentModel(ndb.Model):
	id = ndb.StringProperty()
	name = ndb.StringProperty()
	score = ndb.IntegerProperty()

class NdbTestHandler(webapp2.RequestHandler):
	def list(self):
		q = StudentModel.query()
		q = q.order(StudentModel.id)
		result = q.fetch(999999)
		for s in result:
			self.response.write('%s %s %d<br/>'%(s.id, s.name, s.score))

	def delete(self, id):
		q = StudentModel.query()
		q = q.filter(StudentModel.id == id)
		result = q.fetch(1)
		if len(result) == 0:
			self.response.write('no instance')
			return
		result[0].key.delete()
		self.response.write( 'delete id = %s'%(id) )

	def update(self, id, score_str):
		score = int(score_str)
		q = StudentModel.query()
		q = q.filter(StudentModel.id == id)
		result = q.fetch(1)
		if len(result) == 0:
			self.response.write('no instance')
			return
		result[0].score = score
		result[0].put()
		self.response.write( 'update id = %s'%(id) )

	def add(self, id, name, score):
		model = StudentModel(
		parent=ndb.Key("student", '*notitle*'),
			id = id,
			name = name,
			score = int(score)
		)
		model.put()
		self.response.write( 'add %s, %s, %s'%(id, name, score) )

	def query(self, id):
		q = StudentModel.query()
		q = q.filter(StudentModel.id == id)
		result = q.fetch(1)
		if len(result) == 0:
			self.response.write('nothing')
			return
		s = result[0]
		self.response.write('%s %s %d<br/>'%(s.id, s.name, s.score))

	def get(self, sub_url):
		if sub_url == 'list':
			self.list()
		elif sub_url == 'delete':
			id = self.request.get("id")
			self.delete(id)
		elif sub_url == 'add':
			id = self.request.get("id")
			name = self.request.get("name")
			score = self.request.get("score")
			self.add(id, name, score)
		elif sub_url == 'update':
			id = self.request.get("id")
			score = self.request.get("score")
			self.update(id, score)
		elif sub_url == 'query':
			id = self.request.get("id")
			self.query(id)

app = webapp2.WSGIApplication([
	('/', MainHandler),
    ('/ndb/(\w+)', NdbTestHandler),
], debug=True)

```


{% include JB/setup %}
