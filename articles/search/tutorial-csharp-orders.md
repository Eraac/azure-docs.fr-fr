---
title: Tutoriel C# sur le classement des résultats - Recherche Azure
description: Ce tutoriel s’appuie sur le projet « Pagination des résultats de la recherche - Recherche Azure » pour ajouter le classement des résultats de la recherche. Découvrez comment classer les résultats sur une propriété principale et, pour les résultats qui ont la même propriété principale, comment classer les résultats sur une propriété secondaire. Enfin, découvrez comment classer les résultats en fonction d’un profil de score.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466543"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>Tutoriel C# : Classer les résultats - Recherche Azure

Jusqu’à présent, dans notre série de tutoriels, les résultats sont retournés et affichés dans un ordre par défaut. Il peut s’agit de l’ordre dans lequel se trouvent les données ou bien dépendant d’un éventuel _profil de score_ qui a été défini et qui est utilisé quand aucun paramètre de classement n’est spécifié. Dans ce tutoriel, nous allons découvrir comment classer les résultats en fonction d’une propriété principale puis, pour les résultats qui ont la même propriété principale, comment classer cette sélection sur une propriété secondaire. Comme alternative au classement sur la base de valeurs numériques, le dernier exemple montre comment classer selon un profil de score personnalisé. Nous allons également explorer plus avant l’affichage de _types complexes_.

Afin de comparer facilement les résultats retournés, ce projet s’appuie sur le projet avec défilement infini créé dans le [Tutoriel C# : Pagination des résultats de la recherche - Recherche Azure](tutorial-csharp-paging.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Classer des résultats en fonction d’une seule propriété
> * Classer des résultats en fonction de plusieurs propriétés
> * Filtrer les résultats en fonction de la distance d’un point géographique
> * Classer des résultats en fonction d’un profil de score

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

Disposer de la version avec défilement infini opérationnelle du [Tutoriel C# : Pagination des résultats de la recherche - Recherche Azure](tutorial-csharp-paging.md). Ce projet peut être votre propre version ou vous pouvez l’installer à partir de GitHub : [Créer votre première application](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Classer des résultats en fonction d’une seule propriété

Quand nous classons des résultats en fonction d’une seule propriété, par exemple l’évaluation des hôtels, nous voulons non seulement obtenir les résultats classés, mais aussi la confirmation que l’ordre est bien correct. En d’autres termes, si nous classons sur l’évaluation, nous devons afficher l’évaluation dans la vue.

Dans ce tutoriel, nous allons également ajouter un peu plus d’informations à l’affichage des résultats : le prix de la chambre la moins chère et celui de la chambre la plus chère pour chaque hôtel. Comme nous nous intéressons au classement, nous allons également ajouter des valeurs pour garantir que ce sur quoi nous classons figure aussi dans la vue.

Il n’est pas nécessaire de modifier les modèles pour activer le classement. La vue et le contrôleur doivent être mis à jour. Commencez par ouvrir le contrôleur home.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Ajouter la propriété OrderBy aux paramètres de recherche

1. Pour classer les résultats en fonction d’une seule propriété numérique, il suffit de définir le paramètre **OrderBy** sur le nom de la propriété. Dans la méthode **Index(SearchData model)** , ajoutez la ligne suivante aux paramètres de recherche.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > L’ordre par défaut est croissant, même si vous pouvez ajouter **asc** à la propriété pour rendre cela explicite. Vous spécifiez un ordre décroissant en ajoutant **desc**.

2. Maintenant, exécutez l’application et entrez un terme de recherche courant. Les résultats peuvent ou non être dans l’ordre correct, car ni vous en tant que développeur ni l’utilisateur n’avez un moyen simple de vérifier les résultats !

3. Nous allons faire apparaître que les résultats sont classés sur l’évaluation. Remplacez d’abord les classes **box1** et **box2** dans le fichier hotels.css par les classes suivantes (ces classes sont les seules nouvelles classes dont nous avons besoin pour ce tutoriel).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >Les navigateurs mettent généralement en cache les fichiers css : ceci peut faire qu’un ancien fichier css est utilisé et que vos modifications sont ignorées. Un bon moyen de contourner ceci est d’ajouter au lien une chaîne de requête avec un paramètre de version. Par exemple :
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Mettez à jour le numéro de version si vous pensez qu’un ancien fichier css est utilisé par votre navigateur.

4. Ajoutez la propriété **Rating** (Évaluation) au paramètre **Select** dans la méthode **Index(SearchData model)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Ouvrez la vue (index.cshtml) et remplacez la boucle de rendu ( **&lt;!-- Show the hotel data. --&gt;** ) par le code suivant.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. L’évaluation doit être disponible dans la première page affichée et dans les pages suivantes qui sont appelées via le défilement infini. Pour cette dernière situation, nous devons mettre à jour à la fois l’action **Next** dans le contrôleur et la fonction **scrolled** dans la vue. En commençant par le contrôleur, remplacez la méthode **Next** par le code suivant. Ce code crée et communique le texte de l’évaluation.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Maintenant, mettez à jour la fonction **scrolled** dans la vue, de façon à afficher le texte de l’évaluation.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Vous pouvez maintenant réexécuter l’application. Recherchez un terme courant, comme « wifi », et vérifiez que les résultats sont classés par ordre décroissant de l’évaluation des hôtels.

    ![Classement basé sur l’évaluation](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Vous remarquerez que plusieurs hôtels ont une évaluation identique : leur apparition dans l’affichage correspond donc à nouveau à l’ordre dans lequel les données sont trouvées, ce qui est arbitraire.

    Avant de passer à l’ajout d’un deuxième niveau de classement, ajoutons du code pour afficher la plage de prix des chambres. Nous ajoutons ce code pour deux raisons : montrer l’extraction de données à partir d’un _type complexe_ et présenter le classement des résultats en fonction des prix (peut-être le moins cher en premier).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Ajouter la plage de prix des chambres à la vue

1. Ajoutez les propriétés qui contiennent le prix de la chambre la moins chère et la plus chère au modèle Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Calculez les prix des chambres à la fin de l’action **Index(SearchData model)** dans le contrôleur home. Ajoutez les calculs après le stockage des données temporaires.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Ajoutez la propriété **Rooms** au paramètre **Select** dans la méthode d’action **Index(SearchData model)** du contrôleur.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Modifiez la boucle de rendu de la vue pour afficher la plage des prix pour la première page de résultats.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Changez la méthode **Next** du contrôleur home de façon à communiquer la plage de prix pour les pages de résultats suivantes.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Mettez à jour la fonction **scrolled** dans la vue, de façon à gérer le texte des prix des chambres.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Exécutez l’application et vérifiez que les plages de prix des chambres sont affichés.

    ![Affichage des plages de prix des chambres](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

La propriété **OrderBy** des paramètres de recherche n’acceptera pas d’entrée comme **Rooms.BaseRate** pour fournir le prix de la chambre la moins chère, même si les chambres étaient déjà triées sur le prix (ce qui n’est pas le cas). Pour afficher les hôtels de l’exemple de jeu de données, classés en fonction du prix des chambres, il faudrait de trier les résultats dans votre contrôleur home et envoyer ces résultats à la vue dans l’ordre souhaité.

## <a name="order-results-based-on-multiple-values"></a>Classer des résultats en fonction de plusieurs valeurs

La question est maintenant de savoir comment distinguer entre les hôtels ayant la même évaluation. Un bon moyen serait de classer en fonction de la date de la dernière rénovation de l’hôtel. En d’autres termes, plus la rénovation de l’hôtel est récente, plus l’hôtel apparaît haut dans les résultats.

1. Pour ajouter un deuxième niveau de classement, modifiez les propriétés **OrderBy** et **Select** dans la méthode **Index(SearchData model)** pour y inclure la propriété  **LastRenovationDate**.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Vous pouvez entrer un nombre quelconque de propriétés dans la liste **OrderBy**. Si des hôtels avaient le même classement et la même date de rénovation, une troisième propriété pourrait ainsi être entrée pour les distinguer.

2. Là encore, nous avons besoin de voir la date de rénovation dans la vue, juste pour être certain que le classement est correct. Pour quelque chose comme une rénovation, il est probable que seule l’année soit nécessaire. Remplacez la boucle de rendu de la vue par le code suivant.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Modifiez la méthode **Next** du contrôleur home pour transférer le composant « année » de la dernière date de rénovation.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Changez la fonction **scrolled** dans la vue de façon à afficher le texte de la rénovation.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Exécutez l'application. Effectuez une recherche sur un terme courant, comme « pool » ou « view », et vérifiez que les hôtels avec la même évaluation sont maintenant affichés dans l’ordre décroissant de la date de rénovation.

    ![Classement sur la date de rénovation](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrer les résultats en fonction de la distance d’un point géographique

L’évaluation et la date de rénovation, sont des exemples de propriétés dont l’affichage par ordre décroissant est ce qui convient le mieux. Une liste alphabétique serait un exemple d’une bonne utilisation de l’ordre croissant (par exemple, s’il n’y avait qu’une propriété **OrderBy** et si elle était définie sur **HotelName**, les données apparaîtraient par ordre alphabétique). Cependant, pour nos exemples de données, la distance depuis un point géographique serait plus appropriée.

Pour afficher les résultats en fonction de la distance géographique, plusieurs étapes sont nécessaires.

1. Excluez tous les hôtels qui sont en dehors d’un rayon spécifié à partir du point donné en entrant un filtre avec les paramètres de longitude, de latitude et de rayon. La longitude est donnée en premier à la fonction POINT. Le rayon est exprimé en kilomètres.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Le filtre ci-dessus ne classe _pas_ les résultats en fonction de la distance, il supprime simplement les valeurs non conformes. Pour classer les résultats, entrez un paramètre **OrderBy** qui spécifie la méthode geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Bien que les résultats aient été retournés par Recherche Azure avec un filtre de distance, la distance calculée entre les données et le point spécifié n’est _pas_ retournée. Recalculez cette valeur dans la vue ou dans le contrôleur si vous voulez l’afficher dans les résultats.

    Le code suivant calcule la distance entre deux points définis par leur latitude/longitude.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Vous devez à présent relier ces concepts. Cependant, ces extraits de code s’éloignent de l’objectif de notre tutoriel : la création d’une application basée sur une carte est laissée au lecteur à titre d’exercice. Pour aller plus loin avec cet exemple, considérez l’entrée d’un nom de vile avec un rayon, ou la localisation d’un point sur une carte et la sélection d’un rayon. Pour examiner ces options en détail, consultez les ressources suivantes :

* [Documentation sur Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Rechercher une adresse à l’aide du service Recherche Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Classer des résultats en fonction d’un profil de score

Les exemples donnés jusqu’à présent dans le tutoriel montrent comment classer sur des valeurs numériques (évaluation, date de rénovation, distance géographique), fournissant un processus de classement _exact_. Cependant, certaines recherches et certaines données ne se prêtent pas à cette comparaison facile entre deux éléments de données. Recherche Azure inclut le concept de _scoring_. Des _profils de score_ peuvent être spécifiés pour un jeu de données qui peut être utilisé pour fournir des comparaisons plus complexes et qualitatives, ce qui doit être plus utile par exemple lors de la comparaison de données texte pour déterminer ce qui doit être affiché en premier.

Les profils de score ne sont pas définis par les utilisateurs, mais ils le sont en général par les administrateurs d’un jeu de données. Plusieurs profils de score ont été définis sur les données des hôtels. Voyons comment un profil de score est défini, puis essayons d’écrire du code pour effectuer des recherches sur ces profils.

### <a name="how-scoring-profiles-are-defined"></a>Comment les profils de score sont définis

Examinons trois exemples de profils de score et regardons comment chacun _devrait_ affecter l’ordre des résultats. En tant que développeur d’applications, vous n’écrivez pas ces profils : ils sont écrits par l’administrateur des données. Il est cependant utile d’en examiner la syntaxe.

1. Ceci est le profil de score par défaut pour le jeu de données des hôtels, utilisé quand vous ne spécifiez pas de paramètre **OrderBy** ou **ScoringProfile**. Ce profil améliore le _score_ pour un hôtel si le texte recherché est présent dans le nom, la description ou la liste d’étiquettes (équipements) de l’hôtel. Notez comment les pondérations du score favorisent certains champs. Si le texte de recherche apparaît dans un autre champ, non listé ci-dessous, il aura une pondération de 1. Bien sûr, plus le score est élevé, plus un résultat apparait haut dans la vue.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Le profil de score suivant améliore le score de façon significative si un paramètre fourni contient un ou plusieurs des éléments de la liste d’étiquettes (que nous appelons « équipements »). Le point clé de ce profil est qu’un paramètre _doit_ être fourni, contenant du texte. Si le paramètre est vide ou n’est pas fourni, une erreur est levée.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. Dans ce troisième exemple, l’évaluation donne une amélioration significative au score. La date de la dernière rénovation augmente également le score, mais seulement si cette donnée n’est pas antérieure de plus de 730 jours (2 ans) à la date actuelle.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Maintenant, voyons si ces profils fonctionnent comme nous le pensons !

### <a name="add-code-to-the-view-to-compare-profiles"></a>Ajouter du code à la vue pour comparer les profils

1. Ouvrez le fichier index.cshtml et remplacez la section &lt;body&gt; par le code suivant.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Ouvrez le fichier SearchData.cs et remplacez la classe **SearchData** par le code suivant.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Ouvrez le fichier hotels.css et ajoutez les classes HTML suivantes.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Ajouter du code au contrôleur pour spécifier un profil de score

1. Ouvrez le fichier du contrôleur home. Ajoutez l’instruction **using** suivante (pour faciliter la création de listes).

    ```cs
    using System.Linq;
    ```

2.  Pour cet exemple, nous avons besoin de l’appel initial à **Index** pour en faire un peu plus que retourner seulement la vue initiale. La méthode recherche maintenant jusqu’à 20 équipements à afficher dans la vue.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. Nous avons besoin de deux méthodes privées pour enregistrer les facettes dans un stockage temporaire, et pour les récupérer auprès d’un stockage temporaire et remplir un modèle.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. Nous devons définir les paramètres **OrderBy** et **ScoringProfile** en fonction des besoins. Remplacez la méthode **Index(SearchData model)** existante par ce qui suit.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Lisez les commentaires pour chacune des sélections de **switch**.

5. Nous n’avons pas besoin d’apporter des modifications à l’action **Next** si vous avez ajouté le code supplémentaire pour la section précédente sur le classement basé sur plusieurs propriétés.

### <a name="run-and-test-the-app"></a>Exécuter et tester l’application

1. Exécutez l'application. Vous devez voir un ensemble complet d’équipements dans la vue.

2. Pour le classement, sélectionner « By numerical Rating » (Par évaluation numérique) vous donnera le classement numérique que vous avez déjà implémenté dans ce tutoriel, avec la date de rénovation déterminant le classement des hôtels ayant la même évaluation.

![Classement de « beach » (plage) basé sur l’évaluation](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Essayez maintenant le profil « By amenities » (Par équipements). Effectuez différentes sélections d’équipements et vérifiez que les hôtels avec ces équipements sont promus en haut de la liste des résultats.

![Classement de « beach » (plage) basé sur le profil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Essayez « By Renovated date/Rating profile » (Par profil de Date de rénovation/Évaluation) pour voir si vous obtenez ce que vous attendez. Seuls les hôtels rénovés récemment doivent obtenir une amélioration _freshness_ (fraîcheur).

### <a name="resources"></a>Ressources

Pour plus d’informations, consultez [Ajouter des profils de score à un index Recherche Azure](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Éléments importants à retenir

Retenez les points importants suivants de ce projet :

* Les utilisateurs s’attendent à ce que les résultats de la recherche soient classés, les plus pertinents apparaissant en premier.
* Les données doivent être structurées pour que le classement soit facile. Nous n’avons pas pu effectuer facilement un tri sur « cheapest » (le moins cher) pour commencer, car les données ne sont pas structurées pour permettre un classement sans du code supplémentaire.
* Il peut y avoir de nombreux niveaux de classement pour faire la distinction entre les résultats qui ont la même valeur à un niveau plus élevé du classement.
* Il est naturel que certains résultats soient classés par ordre croissant (par exemple la distance séparant d’un point) et certains autres par ordre décroissant (par exemple l’évaluation des invités).
* Des profils de score peuvent être définis quand des comparaisons numériques ne sont pas disponibles ou pas assez pertinentes pour un jeu de données. Attribuer un score à chaque résultat aide à classer et à afficher les résultats de façon intelligente.

## <a name="next-steps"></a>Étapes suivantes

Vous avez terminé cette série de tutoriels C# et vous avez normalement acquis une bonne connaissance des API de Recherche Azure.

Pour plus d’informations et des tutoriels, parcourez [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) ou les autres tutoriels de la [Documentation de Recherche Azure](https://docs.microsoft.com/azure/search/).
