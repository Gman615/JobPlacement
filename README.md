# Python Live Project
As part of my coding boot camp through the Tech Academy, we worked on a live project with other peers creating a website through the web framework Django. I learn the most by doing hands on work and interacting with others, and this project gave me great experience there. I also was able to learn how to clean up my code, create an API, and CRUD functionalty. We were given abstractions to create functionality for our personal site/application and were required to research how to do these on our own. Through this section of my camp, I was able to develop more self-assurance in my coding abilities. 

This document shows code snippets and a description of the live project done through the Tech Academy.

# Views.py

This section of code shows my views section from my Django Application with CRUD functionality, and an API.

    from django.shortcuts import render, redirect, get_object_or_404<br>
    from .forms import TVshowsForm<br>
    from .models import TVshows<br>
    from django.core.paginator import Paginator<br>
    import requests<br>



    *this renders the home page*<br>
    def TVshow_home(request):<br>
        return render(request, "TVshowAPP/home.html")<br>



    def addShow(request):<br>
        *adds data to the database*<br>
        form = TVshowsForm(request.POST or None)<br>
        if form.is_valid():<br>
            form.save()<br>
            return redirect('TVshow_home')<br>
        else:<br>
            print(form.errors)<br>
            form = TVshowsForm()<br>
        context = {<br>
            'form': form,<br>
        }<br>
        return render(request, 'TVshowAPP/AddTVShow.html', context)<br>

    def seeShow(request):<br>
        *gets all items from DB and returns info to table on TVshow_list*<br>
        tvshow = TVshowsForm()<br>
        tvshowdb = TVshows.objects.all()<br>
        *creates pagination variables*<br>
        page = Paginator(tvshowdb, 10)<br>
        page_show = request.GET.get('page')<br>
        page2 = page.get_page(page_show)<br>
        context = {'tvshow': tvshow, 'tvshowdb': tvshowdb}<br>
        return render(request, 'TVshowAPP/TVShow_List.html', {'page2': page2}, context)<br>



    def showDetails(request, pk):<br>
         *this function locates the pk for each database entry, sends the info to the details page*<br>
        pk = int(pk)<br>
        tvshow = get_object_or_404(TVshows, pk=pk)<br>
        tvform = TVshowsForm(data=request.POST or None, instance=tvshow)<br>
        if request.method == 'POST':<br>
            if tvform.is_valid():<br>
                tvform.save()<br>
                return redirect('seeShow')<br>
            else:<br>
                print(tvform.errors)<br>
        else:<br>
            return render(request, 'TVshowAPP/TVshow_details.html', {'tvform': tvform})<br>

    def editShow(request, pk):<br>
        *allows information to be edited from form for specific show, saves edit as well.*<br>    
        pk = int(pk)<br>
        tvshow = get_object_or_404(TVshows, pk=pk)<br>
        tvform = TVshowsForm(data=request.POST or None, instance=tvshow)<br>
        if request.method == 'POST':<br>
            if tvform.is_valid():<br>
                tvform.save()<br>
                return redirect('seeShow')<br>
            else:<br>
                print(tvform.errors)<br>
        else:<br>
            return render(request, 'TVshowAPP/TVshow_edit.html', {'tvform': tvform})<br>
    
    *function below begins the delete process, sends the user to a page that asks to confirm deletion*<br>
    def deleteShow(request, pk):<br>

        pk = int(pk)<br>
        show = get_object_or_404(TVshows, pk=pk)<br>
        if request.method == 'POST':<br>
            show.delete()<br>
            return redirect('seeShow')<br>
        context = {'show': show}<br>
        return render(request, 'TVshowAPP/TVshow_delete.html', context)<br>

    *function below deletes added data once the user requests to do so*<br>

    def confirmDelete(request):<br>

        if request.method == 'POST':<br>
            tvform = TVshowsForm(request.POST or None)<br>
            if tvform.is_valid():<br>
                tvform.delete()<br>
                return redirect('TVshow_home')<br>
        else:<br>
            return redirect('TVshowAPP/TVshow_List.html')<br>

    *the below functions help create the API with imdb that allows the user to search imdb and display results to the console*<br>

    def TVshow_api(request):<br>

        return render(request, 'TVshowAPP/TVshow_api.html')<br>

    def imdb(request):<br>

        result = {}<br>
        if 'title' in request.GET:<br>
            title = request.GET['title']<br><br>

            url = "https://movie-database-imdb-alternative.p.rapidapi.com/"<br><br>

            querystring = {"s": title, "page": "1", "r": "json"}<br><br>

            headers = {<br>
                'x-rapidapi-key': "b5a6dc6914msh268617fe992e521p1e1e0cjsn6a2da41f88ba",<br>
                'x-rapidapi-host': "movie-database-imdb-alternative.p.rapidapi.com"<br>
            }<br><br>

            response = requests.request("GET", url, headers=headers, params=querystring)<br><br>

            print(response.text)<br>
            print("test")<br>
        return render(request, 'TVshowAPP/TVshow_api.html', {'result': result})<br>
