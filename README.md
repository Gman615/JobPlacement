# Python Live Project
As part of my coding boot camp through the Tech Academy, we worked on a live project with other peers creating a website through the web framework Django. I learn the most by doing hands on work and interacting with others, and this project gave me great experience there. I also was able to learn how to clean up my code, create an API, and CRUD functionalty. We were given abstractions to create functionality for our personal site/application and were required to research how to do these on our own. Through this section of my camp, I was able to develop more self-assurance in my coding abilities. 

This document shows code snippets and a description of the live project done through the Tech Academy.

# Views.py

This section of code shows my views section from my Django Application with CRUD functionality, and an API.

    from django.shortcuts import render, redirect, get_object_or_404
    from .forms import TVshowsForm
    from .models import TVshows
    from django.core.paginator import Paginator
    import requests



    *this renders the home page*
    def TVshow_home(request):
        return render(request, "TVshowAPP/home.html")



    def addShow(request):
        *adds data to the database*
        form = TVshowsForm(request.POST or None)
        if form.is_valid():
            form.save()
            return redirect('TVshow_home')
        else:
            print(form.errors)
            form = TVshowsForm()
        context = {
            'form': form,
        }
        return render(request, 'TVshowAPP/AddTVShow.html', context)

    def seeShow(request):
        *gets all items from DB and returns info to table on TVshow_list*
        tvshow = TVshowsForm()
        tvshowdb = TVshows.objects.all()
        *creates pagination variables*
        page = Paginator(tvshowdb, 10)
        page_show = request.GET.get('page')
        page2 = page.get_page(page_show)
        context = {'tvshow': tvshow, 'tvshowdb': tvshowdb}
        return render(request, 'TVshowAPP/TVShow_List.html', {'page2': page2}, context)



    def showDetails(request, pk):
         *this function locates the pk for each database entry, sends the info to the details page*
        pk = int(pk)
        tvshow = get_object_or_404(TVshows, pk=pk)
        tvform = TVshowsForm(data=request.POST or None, instance=tvshow)
        if request.method == 'POST':
            if tvform.is_valid():
                tvform.save()
                return redirect('seeShow')
            else:
                print(tvform.errors)
        else:
            return render(request, 'TVshowAPP/TVshow_details.html', {'tvform': tvform})

    def editShow(request, pk):
        *allows information to be edited from form for specific show, saves edit as well.*    
        pk = int(pk)
        tvshow = get_object_or_404(TVshows, pk=pk)
        tvform = TVshowsForm(data=request.POST or None, instance=tvshow)
        if request.method == 'POST':
            if tvform.is_valid():
                tvform.save()
                return redirect('seeShow')
            else:
                print(tvform.errors)
        else:
            return render(request, 'TVshowAPP/TVshow_edit.html', {'tvform': tvform})
    
    *function below begins the delete process, sends the user to a page that asks to confirm deletion*
    def deleteShow(request, pk):

        pk = int(pk)
        show = get_object_or_404(TVshows, pk=pk)
        if request.method == 'POST':
            show.delete()
            return redirect('seeShow')
        context = {'show': show}
        return render(request, 'TVshowAPP/TVshow_delete.html', context)

    *function below deletes added data once the user requests to do so*

    def confirmDelete(request):

        if request.method == 'POST':
            tvform = TVshowsForm(request.POST or None)
            if tvform.is_valid():
                tvform.delete()
                return redirect('TVshow_home')
        else:
            return redirect('TVshowAPP/TVshow_List.html')

    *the below functions help create the API with imdb that allows the user to search imdb and display results to the console*

    def TVshow_api(request):

        return render(request, 'TVshowAPP/TVshow_api.html')

    def imdb(request):

        result = {}
        if 'title' in request.GET:
            title = request.GET['title']

            url = "https://movie-database-imdb-alternative.p.rapidapi.com/"

            querystring = {"s": title, "page": "1", "r": "json"}

            headers = {
                'x-rapidapi-key': "b5a6dc6914msh268617fe992e521p1e1e0cjsn6a2da41f88ba",
                'x-rapidapi-host': "movie-database-imdb-alternative.p.rapidapi.com"
            }

            response = requests.request("GET", url, headers=headers, params=querystring)

            print(response.text)
            print("test")
        return render(request, 'TVshowAPP/TVshow_api.html', {'result': result})
