# Filterring querry via get_queryset() method in function:

```python
class TaskViewSet(viewsets.ModelViewSet, generics.RetrieveAPIView):
    permission_classes = [IsAuthenticated]
    serializer_class = TaskSerializer
    queryset = Task.objects.all()

    def get_queryset(self):
        return Task.objects.filter(user=self.request.user)
```
